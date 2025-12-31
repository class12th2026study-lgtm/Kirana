<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>मेरा किराना स्टोर - ऑनलाइन ऑर्डर</title>
    <style>
        body { font-family: Arial, sans-serif; background: #f0f0f0; margin: 0; padding: 0; color: #333; }
        header { background: #2c3e50; color: white; padding: 1rem; text-align: center; position: relative; }
        #cart-icon { position: absolute; top: 1rem; right: 1rem; background: #e74c3c; padding: 0.5rem 1rem; border-radius: 20px; cursor: pointer; }
        h1 { margin: 0; }
        #items { display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 1.5rem; padding: 2rem; }
        .item { background: white; border-radius: 10px; overflow: hidden; box-shadow: 0 4px 8px rgba(0,0,0,0.1); text-align: center; padding: 1rem; }
        .item img { width: 100%; height: 180px; object-fit: cover; border-radius: 8px; }
        .item h3 { margin: 1rem 0 0.5rem; font-size: 1.1rem; }
        .item p { font-weight: bold; color: #27ae60; }
        .mode-btns { display: flex; justify-content: center; gap: 0.5rem; margin: 0.5rem 0; }
        .mode-btn { padding: 0.5rem 1rem; background: #ecf0f1; border-radius: 5px; cursor: pointer; font-size: 0.9rem; }
        .mode-btn.active { background: #3498db; color: white; }
        .qty-input { width: 100%; padding: 0.8rem; margin: 0.5rem 0; border: 1px solid #bdc3c7; border-radius: 5px; }
        .add-btn { background: #27ae60; color: white; border: none; padding: 0.8rem; width: 100%; border-radius: 5px; cursor: pointer; font-weight: bold; }
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); justify-content: center; align-items: center; }
        .modal-content { background: white; padding: 2rem; border-radius: 10px; width: 90%; max-width: 500px; max-height: 80vh; overflow-y: auto; }
        .close { float: right; font-size: 2rem; cursor: pointer; }
        .cart-item { display: flex; justify-content: space-between; padding: 0.5rem 0; border-bottom: 1px solid #eee; }
        input[type=text], input[type=email], input[type=tel] { width: 100%; padding: 0.8rem; margin: 0.5rem 0; border: 1px solid #bdc3c7; border-radius: 5px; }
        #order-btn { background: #e74c3c; color: white; border: none; padding: 1rem; width: 100%; border-radius: 5px; cursor: pointer; font-size: 1.1rem; margin-top: 1rem; }
    </style>
</head>
<body>
    <header>
        <h1>मेरा किराना स्टोर</h1>
        <div id="cart-icon">कार्ट (<span id="cart-count">0</span>)</div>
    </header>

    <section id="items"></section>

    <div id="cart-modal" class="modal">
        <div class="modal-content">
            <span class="close">&times;</span>
            <h2>आपका कार्ट</h2>
            <div id="cart-items"></div>
            <p style="font-size:1.2rem;font-weight:bold;margin-top:1rem;">कुल राशि: ₹<span id="cart-total">0</span></p>
            
            <input type="text" id="customer-name" placeholder="आपका नाम">
            <input type="email" id="customer-email" placeholder="ईमेल (ऑर्डर डिटेल्स के लिए)">
            <input type="tel" id="customer-phone" placeholder="फोन नंबर">
            <input type="text" id="customer-address" placeholder="पता (डिलीवरी के लिए)">
            
            <button id="order-btn">ऑर्डर कन्फर्म करें</button>
            <p style="font-size:0.9rem;margin-top:1rem;color:#7f8c8d;">ऑर्डर करने के बाद आपको WhatsApp/ईमेल पर बिल मिलेगा और पेमेंट लिंक भेजा जाएगा।</p>
        </div>
    </div>

    <script>
        const items = [
            { name: "जवाइन / Javain", price: 300, unit: "kg", img: "https://www.thespruceeats.com/thmb/IG9YmIUuRybLjS8Lh2Fz3gTmUTQ=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/Mustardseeds-GettyImages-175494643-593863933df78c537b14c2cd.jpg" },
            { name: "मंगरैल / Mangrail", price: 350, unit: "kg", img: "https://lookaside.fbsbx.com/lookaside/crawler/media/?media_id=10160218397867163" },
            { name: "मेथी / Methi", price: 120, unit: "kg", img: "https://www.metropolisindia.com/upgrade/blog/upload/25/06/benefits-of-methi-seeds1750231786.webp" },
            { name: "सौफ / Saunf", price: 280, unit: "kg", img: "https://www.thespruceeats.com/thmb/0Lchi3YDRpkuKIseF1BUJjTYepc=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/GettyImages-562938349-58920a845f9b5874ee9f872b.jpg" },
            { name: "तील / Til", price: 280, unit: "kg", img: "https://i.ebayimg.com/images/g/-sAAAOSwOL5ng8G6/s-l1200.jpg" },
            { name: "गड़ी / Gadi", price: 250, unit: "kg", img: "https://i.ebayimg.com/images/g/EVkAAOSwKOVlam7N/s-l1200.jpg" },
            { name: "किसमिस / Kismis", price: 580, unit: "kg", img: "http://ajfan.store/cdn/shop/files/Indian_Kismis.webp?v=1741590461" },
            { name: "काजु / Kaju", price: 110, unit: "kg", img: "https://i.ebayimg.com/images/g/-XYAAOSw6elg0bwA/s-l400.jpg" },
            { name: "धुप / Dhoop", price: 160, unit: "packet", img: "https://m.media-amazon.com/images/I/717pXyJDIEL._AC_UF350,350_QL80_.jpg" },
            { name: "कपूर / Kapoor", price: 1000, unit: "kg", img: "https://m.media-amazon.com/images/I/711zINIHucL._AC_UF894,1000_QL80_.jpg" },
            { name: "जीरा / Jeera", price: 380, unit: "kg", img: "https://ranibrand.com/cdn/shop/files/81WxdtFG71L_872x700.jpg?v=1704868894" },
            { name: "मरिच / Mirch", price: 900, unit: "kg", img: "https://www.saveur.com/uploads/2019/03/18/ASZ7FC4WVB5SUMXN3SCFMYFS2A.jpg?auto=webp" },
            { name: "धनिया / Dhaniya", price: 130, unit: "kg", img: "https://m.media-amazon.com/images/I/81IzL7CsiQL._AC_UF894,1000_QL80_.jpg" },
            { name: "हल्दी / Haldi", price: 240, unit: "kg", img: "https://m.media-amazon.com/images/I/41m5YU+-KvL._AC_UF894,1000_QL80_.jpg" },
            { name: "बेसन / Besan", price: 110, unit: "kg", img: "https://m.media-amazon.com/images/I/61MeDfOoGRL.jpg_BO30,255,255,255_UF900,850_SR1910,1000,0,C_QL100_.jpg" },
            { name: "सूजी / Suji", price: 40, unit: "kg", img: "https://www.seriouseats.com/thmb/syRT544tlIDwO_wpCc0N7ey9w4Q=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/__opt__aboutcom__coeus__resources__content_migration__serious_eats__seriouseats.com__recipes__images__2012__05__20121405-Sooji-Upma-20Indian-Breakfast-21e04a2320de433eb5906cb71caa2b70.jpg" },
            { name: "मैदा / Maida", price: 38, unit: "kg", img: "https://static.toiimg.com/photo/69146851.cms" },
            { name: "आटा / Aata", price: 34, unit: "kg", img: "https://www.quicklly.com/upload_images/blog/1723788465-atta-vs-whole-wheat-flour:-which-is-best-for-indian-cooking.jpg" },
            { name: "मसूर दाल / Masoor Dal", price: 90, unit: "kg", img: "https://static01.nyt.com/images/2018/01/16/dining/16COOKING-MASOORDAL/16COOKING-MASOORDAL-master768.jpg" },
            { name: "रहर दाल / Arhar Dal", price: 110, unit: "kg", img: "https://www.greendna.in/cdn/shop/files/toor-dal2_1024x1024@2x.jpg?v=1747065927" },
            { name: "मुंगदाल / Moong Dal", price: 140, unit: "kg", img: "https://www.thespruceeats.com/thmb/uOLxhhppFWyR1iCM2Q3jrbaEGPA=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/vegetarian-mung-dhal-recipe-3378496-hero-03-f1c34f2638ff46d9880130cda0c61e3e.jpg" },
            { name: "चिउड़ा / Chiwda", price: 45, unit: "kg", img: "https://manjulaskitchen.com/wp-content/uploads/spicy_chivda_poha.jpg" },
            { name: "स्वाविन / Soyabin", price: 120, unit: "kg", img: "https://global-blog.cpcdn.com/in/2025/07/soy-products-soybean-soyachunk-granule-flatlay.jpg" },
            { name: "लहसून / Lahsun", price: 120, unit: "kg", img: "https://m.media-amazon.com/images/I/41pEs7koGTL._AC_UF894,1000_QL80_.jpg" },
            { name: "मिर्चा पाउडर / Chilli Powder", price: 300, unit: "kg", img: "http://nibsf.com/cdn/shop/articles/Screenshot_2023-11-21_161708.png?v=1700612391" },
            { name: "पास्ता / Pasta", price: 65, unit: "packet", img: "https://5.imimg.com/data5/SELLER/Default/2025/2/490719040/TT/GG/VC/205271116/fusilli-pasta-500x500.jpg" },
            { name: "सरसो / Sarso", price: 140, unit: "kg", img: "https://www.thespruceeats.com/thmb/IG9YmIUuRybLjS8Lh2Fz3gTmUTQ=/1500x0/filters:no_upscale():max_bytes(150000):strip_icc()/Mustardseeds-GettyImages-175494643-593863933df78c537b14c2cd.jpg" },
            { name: "रामदाना / Ram Dana", price: 140, unit: "kg", img: "https://i.ebayimg.com/images/g/bxAAAOSwaVJjsd3s/s-l1200.jpg" },
            { name: "मिश्री / Mishri", price: 80, unit: "kg", img: "https://m.media-amazon.com/images/I/410x6hOmWqL._AC_UF894,1000_QL80_.jpg" },
            { name: "चायपति / Tea", price: 250, unit: "kg", img: "https://patidarrajwadichai.com/wp-content/uploads/2024/07/Masala-Chai-Benefits-856x500.jpg" },
            { name: "हल्दी पाउडर / Haldi Powder", price: 300, unit: "kg", img: "https://m.media-amazon.com/images/I/81-pFYAZkdL._AC_UF350,350_QL80_.jpg" },
            { name: "धनिया पाउडर / Dhaniya Powder", price: 200, unit: "kg", img: "https://m.media-amazon.com/images/I/81cIBbUMd6L._AC_UF350,350_QL80_.jpg" },
            { name: "जीरा पाउडर / Jeera Powder", price: 450, unit: "kg", img: "https://m.media-amazon.com/images/I/81bxkjNsv4L._AC_UF350,350_QL80_.jpg" },
            { name: "मरिच पाउडर / Mirch Powder", price: 1000, unit: "kg", img: "https://i.ebayimg.com/images/g/ZYEAAOSweGJl2xjh/s-l400.jpg" },
            { name: "पंचफोरन / Panchforan", price: 300, unit: "kg", img: "https://chilliandmint.com/wp-content/uploads/2013/10/img_7307.jpg" },
            { name: "इलाइची (छोटी) / Elaichi Small", price: 5000, unit: "kg", img: "https://i.ebayimg.com/images/g/E10AAOSw2dJgfAoW/s-l400.jpg" },
            { name: "इलाइची (बड़ी) / Elaichi Big", price: 3500, unit: "kg", img: "https://www.greendna.in/cdn/shop/products/blackcar_600x.jpg?v=1652703668" },
            { name: "लौंग / Laung", price: 1500, unit: "kg", img: "https://i.ebayimg.com/images/g/pj0AAOSwOAhc34fM/s-l1200.jpg" },
            { name: "जायफर / Jaiphal", price: 8, unit: "piece", img: "https://m.media-amazon.com/images/I/31xCV6f6EYL._AC_UF894,1000_QL80_.jpg" },
            // बाकी आइटम्स के लिए ऊपर जैसे ही इमेज हैं, अगर कुछ मिसिंग लगे तो ऊपर की generic इमेज यूज की है
            { name: "सतुई / Satu", price: 120, unit: "kg", img: "https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgZMKEa_ECdo1Nan7QSDZfgmSORVw_wtIKbuF9ThRLt8HqbzardXr3gBeOT8abJs_OwSwzGLFuSqP7ujdgFQ1FcqWI9OYu1dNDkc8WCW8H_9la-2aOf0GY29QhdFiKpCJ4bq73jipt2eYHp/s1600/roasted+chickepeas+and+sattu.NEF.jpg" },
            { name: "फारचुन 1L / Fortune 1L", price: 155, unit: "L", img: "https://m.media-amazon.com/images/I/510JZdHZZvL.jpg_BO30,255,255,255_UF900,850_SR1910,1000,0,C_PIRIOFOURANDHALF-medium,BottomLeft,30,-20_ZJPHNwYW4gZm9yZWdyb3VuZD0iIzU2NTk1OSIgZm9udD0iQW1hem9uRW1iZXIgNTAiID4zNSw2Mjg8L3NwYW4+,500,900,420,420,0,0_QL100_.jpg" },
            // और बाकी तेल/ऑइल के लिए यही इमेज यूज कर लो या generic
        ];

        let cart = [];

        const itemsContainer = document.getElementById('items');
        items.forEach(item => {
            const div = document.createElement('div');
            div.className = 'item';
            div.innerHTML = `
                <img src="\( {item.img}" alt=" \){item.name}">
                <h3>${item.name}</h3>
                <p>₹${item.price} / ${item.unit}</p>
                <div class="mode-btns">
                    <div class="mode-btn active" data-mode="weight">वजन/g/ml</div>
                    <div class="mode-btn" data-mode="price">पैसे से</div>
                </div>
                <input type="text" class="qty-input" placeholder="जैसे 0.5, 250g, 100">
                <button class="add-btn">कार्ट में डालें</button>
            `;
            const modeBtns = div.querySelectorAll('.mode-btn');
            modeBtns.forEach(btn => btn.addEventListener('click', () => {
                modeBtns.forEach(b => b.classList.remove('active'));
                btn.classList.add('active');
            }));
            div.querySelector('.add-btn').addEventListener('click', () => addToCart(item, div));
            itemsContainer.appendChild(div);
        });

        function addToCart(item, div) {
            const mode = div.querySelector('.mode-btn.active').dataset.mode;
            let input = div.querySelector('.qty-input').value.trim();
            if (!input) return alert('मात्रा डालें');

            let qty = parseFloat(input.replace(/g|ml|l|L/g, ''));
            if (isNaN(qty)) return alert('सही मात्रा डालें');

            if (input.includes('g') && item.unit === 'kg') qty /= 1000;
            if (input.includes('ml') && item.unit === 'L') qty /= 1000;

            let total = mode === 'weight' ? item.price * qty : qty;
            if (mode === 'price') qty = qty / item.price;

            cart.push({name: item.name, qty: qty.toFixed(3), unit: item.unit, rate: item.price, total: total.toFixed(2)});
            updateCart();
            div.querySelector('.qty-input').value = '';
        }

        function updateCart() {
            document.getElementById('cart-count').innerText = cart.length;
            const cartItems = document.getElementById('cart-items');
            cartItems.innerHTML = '';
            let grand = 0;
            cart.forEach((c, i) => {
                grand += parseFloat(c.total);
                const p = document.createElement('div');
                p.className = 'cart-item';
                p.innerHTML = `<span>\( {c.name}<br> \){c.qty} \( {c.unit} × ₹ \){c.rate} = ₹${c.total}</span>
                               <button onclick="cart.splice(${i},1); updateCart();">हटाएं</button>`;
                cartItems.appendChild(p);
            });
            document.getElementById('cart-total').innerText = grand.toFixed(2);
        }

        const modal = document.getElementById('cart-modal');
        document.getElementById('cart-icon').onclick = () => modal.style.display = 'flex';
        document.querySelector('.close').onclick = () => modal.style.display = 'none';

        document.getElementById('order-btn').onclick = () => {
            if (cart.length === 0) return alert('कार्ट खाली है');
            const name = document.getElementById('customer-name').value || 'अज्ञात';
            const email = document.getElementById('customer-email').value || 'noemail';
            const phone = document.getElementById('customer-phone').value || 'nophone';
            const address = document.getElementById('customer-address').value || 'no address';

            let message = `नया ऑर्डर!\nनाम: ${name}\nफोन: ${phone}\nईमेल: ${email}\nपता: ${address}\n\nआइटम्स:\n`;
            cart.forEach(c => message += `${c.name} - ${c.qty} \( {c.unit} = ₹ \){c.total}\n`);
            message += `\nकुल: ₹${document.getElementById('cart-total').innerText}`;

            alert('ऑर्डर कन्फर्म! डिटेल्स कॉपी कर लें या WhatsApp पर भेजें:\n\n' + message);
            // यहां तुम WhatsApp लिंक या ईमेल कर सकते हो, या EmailJS ऐड कर लो बाद में
            cart = [];
            updateCart();
            modal.style.display = 'none';
        };
    </script>
</body>
</html>
