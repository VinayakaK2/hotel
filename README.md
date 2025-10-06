<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>FoodieHub — Mini Food Ordering (with Admin)</title>
  <style>
    /* ---------- Reset & base ---------- */
    * { box-sizing: border-box; margin:0; padding:0; }
    body {
      font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      background: #fafafa;
      color: #111;
      line-height: 1.4;
    }
    a { color: inherit; text-decoration: none; }
    button { font: inherit; }

    /* ---------- Header ---------- */
    header {
      background: linear-gradient(90deg,#ff6b6b,#e23744);
      color:#fff;
      padding: 14px 20px;
      position: sticky;
      top:0;
      z-index: 50;
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:12px;
    }
    .brand { display:flex; align-items:center; gap:12px; }
    .logo {
      width:44px; height:44px; border-radius:9px;
      background: white;
      display:flex; align-items:center; justify-content:center;
      font-weight:700; color:#e23744;
      box-shadow: 0 2px 8px rgba(0,0,0,0.08);
    }
    nav a { margin-left:14px; color:rgba(255,255,255,0.95); font-weight:600; }
    .cart-bubble {
      background: rgba(0,0,0,0.12); color:#fff; padding:6px 10px; border-radius:999px; margin-left:10px;
      font-weight:700;
    }

    /* ---------- Hero ---------- */
    .hero {
      padding: 56px 20px;
      background-image: url('https://images.unsplash.com/photo-1600891964599-f61ba0e24092?w=1600&q=80&auto=format&fit=crop');
      background-size: cover;
      background-position: center;
      color: #fff;
      text-align:center;
      display:flex;
      flex-direction:column;
      gap:12px;
      align-items:center;
      justify-content:center;
    }
    .hero .title { font-size: clamp(22px, 4vw, 36px); font-weight:800; text-shadow:0 6px 30px rgba(0,0,0,0.35); }
    .hero .subtitle { font-size: 16px; opacity:0.95; }
    .search {
      margin-top:12px;
      width: min(900px, 86%);
      display:flex;
      gap:8px;
      align-items:center;
    }
    .search input {
      width:100%;
      padding:12px 16px;
      border-radius:999px;
      border:none;
      outline: none;
      font-size:16px;
      box-shadow: 0 4px 18px rgba(0,0,0,0.12);
    }

    /* ---------- Main layout ---------- */
    main { padding: 28px 18px; max-width:1100px; margin: 0 auto; }

    .section-title { font-size:20px; margin-bottom:12px; color:#222; display:flex; align-items:center; justify-content:space-between; gap:10px; }

    /* ---------- Menu grid ---------- */
    .menu-grid {
      display:grid;
      grid-template-columns: repeat( auto-fit, minmax(220px, 1fr) );
      gap:16px;
    }
    .card {
      background:white; border-radius:12px; overflow:hidden;
      box-shadow: 0 8px 28px rgba(18,18,18,0.06);
      transition: transform .18s ease, box-shadow .18s ease;
      display:flex; flex-direction:column;
    }
    .card:hover { transform: translateY(-6px); box-shadow: 0 18px 48px rgba(18,18,18,0.12); }
    .card img { width:100%; height:140px; object-fit:cover; display:block; }
    .card .body { padding:12px 14px; flex:1; display:flex; flex-direction:column; gap:8px; }
    .card h3 { font-size:16px; }
    .card .price { font-weight:700; color:#e23744; }
    .card .actions { margin-top:auto; display:flex; gap:8px; }
    .btn { padding:10px 12px; border-radius:10px; border:none; cursor:pointer; font-weight:700; }
    .btn-primary { background:#e23744; color:white; flex:1; }
    .btn-ghost { background:transparent; border:1px solid #e8e8e8; color:#444; width:84px; }

    /* ---------- Cart ---------- */
    .cart {
      position: fixed;
      right: 18px;
      bottom: 18px;
      width: 320px;
      max-width: calc(100% - 36px);
      background: white;
      border-radius:14px;
      box-shadow: 0 12px 40px rgba(2,2,2,0.16);
      padding:12px;
      z-index:80;
    }
    .cart h4 { margin-bottom:8px; }
    .cart-list { max-height:270px; overflow:auto; margin-bottom:10px; }
    .cart-item { display:flex; gap:8px; align-items:center; justify-content:space-between; padding:8px 6px; border-bottom:1px dashed #f0f0f0; }
    .qty-controls { display:flex; gap:6px; align-items:center; }
    .small-btn { padding:6px 8px; border-radius:8px; border:1px solid #eee; background:#fafafa; cursor:pointer; }
    .checkout { background:#2d8f5a; color:white; border:none; padding:10px; width:100%; border-radius:10px; font-weight:800; cursor:pointer; }

    /* ---------- Footer ---------- */
    footer { margin-top:40px; padding:18px; text-align:center; color:#777; }

    /* ---------- Admin styles (only shown in admin UI) ---------- */
    .admin-root { padding:20px; max-width:1100px; margin:20px auto; }
    .admin-panel { display:grid; grid-template-columns: 1fr 360px; gap:16px; }
    .panel { background:white; border-radius:12px; padding:14px; box-shadow:0 10px 30px rgba(0,0,0,0.06); }
    .orders-list { max-height:520px; overflow:auto; }
    .order-card { border-bottom:1px solid #f1f1f1; padding:10px 0; }
    .form-row { display:flex; gap:8px; margin-bottom:10px; }
    input[type="text"], input[type="number"], textarea {
      width:100%; padding:10px; border:1px solid #e8e8e8; border-radius:8px; outline:none;
    }
    .danger { background:#fff0f0; color:#b91c1c; border:1px solid #ffd6d6; padding:8px; border-radius:8px; cursor:pointer; }

    /* ---------- Responsive ---------- */
    @media (max-width:900px) {
      .admin-panel { grid-template-columns: 1fr; }
      .cart { right: 12px; left:12px; bottom:12px; width: auto; max-width:unset; }
      header { padding:10px 12px; }
      .search input { width:100%; }
    }
  </style>
</head>
<body>

  <!-- Header -->
  <header>
    <div class="brand">
      <div class="logo">FH</div>
      <div>
        <div style="font-weight:800">FoodieHub</div>
        <div style="font-size:12px; opacity:0.9;">Mini food ordering project</div>
      </div>
    </div>

    <nav id="top-nav">
      <a href="#home">Home</a>
      <a href="#menu">Menu</a>
      <a href="#about">About</a>
      <a href="#contact">Contact</a>
      <span class="cart-bubble" id="cart-count">0</span>
    </nav>
  </header>

  <!-- PUBLIC SITE ROOT -->
  <div id="site-root" style="display:none;">
    <section class="hero" id="home">
      <div class="title">Order delicious food from FoodieHub 🍽️</div>
      <div class="subtitle">Quick delivery • Best deals • Fresh & tasty</div>
      <div class="search" style="margin-top:18px;">
        <input id="search-input" placeholder="Search for dishes or restaurants (type e.g. pizza, coffee)"/>
      </div>
    </section>

    <main>
      <div class="section-title">
        <div>🍽️ Our Menu</div>
        <div style="font-size:13px; color:#666;">Browse and add to cart</div>
      </div>

      <div class="menu-grid" id="menu-grid">
        <!-- cards injected here -->
      </div>

      <div style="margin-top:30px;" id="about">
        <div class="section-title">About Us</div>
        <p style="color:#555;">FoodieHub is a demo mini-project to show a simple food-ordering flow with an admin panel. This is purely for learning and demo purposes.</p>
      </div>

      <div style="margin-top:28px;" id="contact">
        <div class="section-title">Contact</div>
        <form id="contact-form" onsubmit="event.preventDefault(); alert('Message sent (demo)');">
          <div style="display:flex; gap:8px; flex-wrap:wrap;">
            <input style="flex:1; min-width:200px;" placeholder="Your name" required />
            <input style="flex:1; min-width:200px;" type="email" placeholder="Your email" required />
          </div>
          <div style="margin-top:8px;">
            <textarea placeholder="Your message" rows="4" style="width:100%; border-radius:8px; padding:10px; border:1px solid #eee;"></textarea>
          </div>
          <div style="margin-top:8px;">
            <button class="btn btn-primary" type="submit">Send</button>
          </div>
        </form>
      </div>

      <footer>
        © 2025 FoodieHub — Demo Mini Project
      </footer>
    </main>

    <!-- Floating Cart -->
    <div class="cart" id="cart-root" aria-live="polite" style="display:none;">
      <h4>🛒 Your Cart</h4>
      <div class="cart-list" id="cart-list"></div>
      <div style="display:flex; gap:8px; margin-bottom:8px; align-items:center;">
        <div style="font-weight:800; font-size:16px;">Total: ₹<span id="cart-total">0</span></div>
      </div>
      <button class="checkout" id="place-order-btn">Place Order</button>
    </div>
  </div>

  <!-- ADMIN ROOT (rendered only on /admin or #/admin route) -->
  <div id="admin-root" style="display:none;">
    <div class="admin-root">
      <div id="admin-login" class="panel" style="max-width:460px; margin: 0 auto 16px;">
        <h3 style="margin-bottom:10px;">Admin Login</h3>
        <p style="color:#666; margin-bottom:12px;">Enter admin credentials to access the panel. (Demo credentials: <strong>admin / admin123</strong>)</p>
        <input id="admin-user" placeholder="Username" style="margin-bottom:8px;" />
        <input id="admin-pass" type="password" placeholder="Password" style="margin-bottom:8px;" />
        <div style="display:flex; gap:8px;">
          <button class="btn btn-primary" id="admin-login-btn">Login</button>
          <button class="btn btn-ghost" id="admin-to-site">Open public site</button>
        </div>
        <div id="admin-login-msg" style="margin-top:8px;color:#b91c1c;display:none;"></div>
      </div>

      <div id="admin-ui" style="display:none;">
        <div style="display:flex; justify-content:space-between; align-items:center; gap:18px; margin-bottom:12px;">
          <h2>Admin Panel</h2>
          <div style="display:flex; gap:8px;">
            <button class="btn" id="admin-logout" style="background:#ff6b6b;color:white;border-radius:8px;">Logout</button>
            <button class="btn btn-ghost" id="admin-open-site">Open public site</button>
          </div>
        </div>

        <div class="admin-panel">
          <div class="panel">
            <h3 style="margin-bottom:8px;">Manage Menu</h3>

            <div style="margin-bottom:8px;">
              <div class="form-row">
                <input id="new-name" placeholder="Dish name" />
                <input id="new-price" type="number" placeholder="Price (₹)" />
              </div>
              <input id="new-img" placeholder="Image URL (optional)" />
              <div style="margin-top:8px; display:flex; gap:8px;">
                <button class="btn btn-primary" id="add-menu-btn">Add Dish</button>
                <button class="btn btn-ghost" id="reset-menu">Reset to default</button>
              </div>
            </div>

            <div style="margin-top:12px;">
              <div style="font-weight:700; margin-bottom:6px;">Current Menu</div>
              <div id="admin-menu-list" style="max-height:420px; overflow:auto;"></div>
            </div>
          </div>

          <div class="panel">
            <h3 style="margin-bottom:8px;">Orders</h3>
            <div style="font-size:13px; color:#666; margin-bottom:8px;">Placed orders from customers (demo)</div>
            <div class="orders-list" id="orders-list"></div>
            <div style="margin-top:10px;">
              <button id="clear-orders" class="danger" style="width:100%;">Clear All Orders</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

<script>
/* =========================
   Data & Storage helpers
   ========================= */

const DEFAULT_MENU = [
  { name: "Cheese Pizza", price: 199, img: "https://images.unsplash.com/photo-1604908177522-432bb97096a3?w=800&q=60&auto=format&fit=crop" },
  { name: "Veg Burger", price: 99, img: "https://images.unsplash.com/photo-1617196034915-3b83d883f0da?w=800&q=60&auto=format&fit=crop" },
  { name: "White Sauce Pasta", price: 149, img: "https://images.unsplash.com/photo-1617196034915-36b22f09d6ab?w=800&q=60&auto=format&fit=crop" },
  { name: "Choco Ice Cream", price: 79, img: "https://images.unsplash.com/photo-1607082349566-1873425d7e9d?w=800&q=60&auto=format&fit=crop" },
  { name: "French Fries", price: 89, img: "https://images.unsplash.com/photo-1586190848861-99aa4a171e90?w=800&q=60&auto=format&fit=crop" },
  { name: "Cold Coffee", price: 120, img: "https://images.unsplash.com/photo-1527168027773-0cc890c1a0f3?w=800&q=60&auto=format&fit=crop" }
];

const STORAGE_KEYS = {
  MENU: 'foodiehub_menu_v1',
  ORDERS: 'foodiehub_orders_v1',
  ADMIN_AUTH: 'foodiehub_admin_auth_v1'
};

function loadMenu() {
  try {
    const raw = localStorage.getItem(STORAGE_KEYS.MENU);
    if (!raw) return JSON.parse(JSON.stringify(DEFAULT_MENU));
    const parsed = JSON.parse(raw);
    if (!Array.isArray(parsed)) return JSON.parse(JSON.stringify(DEFAULT_MENU));
    return parsed;
  } catch(e) {
    console.error('loadMenu err', e);
    return JSON.parse(JSON.stringify(DEFAULT_MENU));
  }
}
function saveMenu(menu) {
  localStorage.setItem(STORAGE_KEYS.MENU, JSON.stringify(menu));
}

function loadOrders() {
  try {
    const raw = localStorage.getItem(STORAGE_KEYS.ORDERS);
    if (!raw) return [];
    return JSON.parse(raw);
  } catch(e) { return []; }
}
function saveOrders(orders) {
  localStorage.setItem(STORAGE_KEYS.ORDERS, JSON.stringify(orders));
}

function isAdminLoggedIn() {
  return localStorage.getItem(STORAGE_KEYS.ADMIN_AUTH) === "1";
}
function setAdminLoggedIn(val) {
  localStorage.setItem(STORAGE_KEYS.ADMIN_AUTH, val ? "1" : "0");
}

/* =========================
   ROUTING: decide which root to show
   - if URL path contains /admin OR hash === #/admin -> admin root
   - otherwise -> public site
   ========================= */

function routeInit() {
  const path = window.location.pathname || '';
  const hash = window.location.hash || '';
  const isAdminRoute = path.includes('/admin') || hash === '#/admin';

  if (isAdminRoute) {
    showAdminRoot();
  } else {
    showPublicSite();
  }
}

/* =========================
   PUBLIC SITE UI & Cart logic
   ========================= */

let menuData = loadMenu();
let cart = {}; // { "Cheese Pizza": {price, qty} }

const dom = {
  siteRoot: document.getElementById('site-root'),
  menuGrid: document.getElementById('menu-grid'),
  searchInput: document.getElementById('search-input'),
  cartRoot: document.getElementById('cart-root'),
  cartList: document.getElementById('cart-list'),
  cartTotal: document.getElementById('cart-total'),
  cartCount: document.getElementById('cart-count'),
  placeOrderBtn: document.getElementById('place-order-btn')
};

function showPublicSite() {
  document.getElementById('admin-root').style.display = 'none';
  dom.siteRoot.style.display = 'block';
  dom.cartRoot.style.display = 'none';
  renderMenuCards(menuData);
  bindPublicEvents();
  updateCartUI();
}

function bindPublicEvents() {
  dom.searchInput.addEventListener('input', (e) => {
    const q = e.target.value.trim().toLowerCase();
    renderMenuCards(menuData.filter(i => i.name.toLowerCase().includes(q)));
  });

  dom.placeOrderBtn.addEventListener('click', () => {
    placeOrder();
  });
}

function renderMenuCards(list) {
  dom.menuGrid.innerHTML = '';
  list.forEach(item => {
    const card = document.createElement('div');
    card.className = 'card';
    card.innerHTML = `
      <img src="${item.img || ''}" alt="${escapeHtml(item.name)}" />
      <div class="body">
        <h3>${escapeHtml(item.name)}</h3>
        <div style="display:flex;align-items:center;justify-content:space-between;">
          <div class="price">₹${item.price}</div>
          <div style="font-size:13px;color:#666;">Available</div>
        </div>
        <div class="actions">
          <button class="btn btn-primary">Add to Cart</button>
          <button class="btn btn-ghost" onclick="viewDish('${escapeJs(item.name)}')">View</button>
        </div>
      </div>
    `;
    const addBtn = card.querySelector('.btn-primary');
    addBtn.addEventListener('click', () => addToCart(item.name, item.price));
    dom.menuGrid.appendChild(card);
  });
}

// utility to show a simple modal-ish alert for dish view
function viewDish(name) {
  alert('Dish: ' + name + '\\n(This is a demo view.)');
}

/* cart operations */
function addToCart(name, price) {
  if (cart[name]) cart[name].qty++;
  else cart[name] = { price: price, qty: 1 };
  updateCartUI();
}

function decFromCart(name) {
  if (!cart[name]) return;
  cart[name].qty--;
  if (cart[name].qty <= 0) delete cart[name];
  updateCartUI();
}

function removeFromCart(name) {
  delete cart[name];
  updateCartUI();
}

function updateCartUI() {
  // rebuild cart list
  const items = Object.entries(cart);
  const listRoot = dom.cartList;
  listRoot.innerHTML = '';
  let total = 0;
  if (items.length === 0) {
    listRoot.innerHTML = '<div style="color:#666; padding:8px;">Your cart is empty.</div>';
    dom.cartRoot.style.display = 'none';
  } else {
    dom.cartRoot.style.display = 'block';
    items.forEach(([name, obj]) => {
      const row = document.createElement('div');
      row.className = 'cart-item';
      row.innerHTML = `
        <div style="flex:1;">
          <div style="font-weight:700">${escapeHtml(name)}</div>
          <div style="font-size:13px;color:#666">₹${obj.price} each</div>
        </div>
        <div style="display:flex; flex-direction:column; align-items:flex-end; gap:6px;">
          <div class="qty-controls">
            <button class="small-btn" onclick="decFromCart('${escapeJs(name)}')">-</button>
            <div style="padding:6px 8px; border-radius:6px; background:#f4f4f4;">${obj.qty}</div>
            <button class="small-btn" onclick="addToCart('${escapeJs(name)}', ${obj.price})">+</button>
          </div>
          <div style="display:flex; gap:6px; align-items:center;">
            <div style="font-weight:700;">₹${obj.price * obj.qty}</div>
            <button class="small-btn" style="background:#fff;border:1px solid #ffdede;color:#b91c1c;" onclick="removeFromCart('${escapeJs(name)}')">Remove</button>
          </div>
        </div>
      `;
      listRoot.appendChild(row);
      total += obj.price * obj.qty;
    });
  }
  dom.cartTotal.textContent = total;
  dom.cartCount.textContent = items.reduce((s,[,o])=>s+o.qty,0);
}

/* Place order -> saves to localStorage (orders) and clears cart */
function placeOrder() {
  const items = Object.entries(cart);
  if (items.length === 0) { alert('Cart is empty'); return; }
  const orders = loadOrders();
  const timestamp = Date.now();
  const order = {
    id: 'ORD' + timestamp,
    createdAt: timestamp,
    items: items.map(([name, o]) => ({ name, price: o.price, qty: o.qty })),
    total: items.reduce((s,[,o])=>s + o.price * o.qty, 0),
    status: 'Placed'
  };
  orders.unshift(order); // newest first
  saveOrders(orders);
  cart = {};
  updateCartUI();
  alert('Order placed! (demo) — Admin can view it in /admin');
}

/* =========================
   ADMIN UI & logic
   ========================= */

const admin = {
  root: document.getElementById('admin-root'),
  loginBox: document.getElementById('admin-login'),
  loginBtn: document.getElementById('admin-login-btn'),
  userInput: document.getElementById('admin-user'),
  passInput: document.getElementById('admin-pass'),
  loginMsg: document.getElementById('admin-login-msg'),
  adminUI: document.getElementById('admin-ui'),
  menuListRoot: document.getElementById('admin-menu-list'),
  ordersListRoot: document.getElementById('orders-list'),
  addMenuBtn: document.getElementById('add-menu-btn'),
  newNameInput: document.getElementById('new-name'),
  newPriceInput: document.getElementById('new-price'),
  newImgInput: document.getElementById('new-img'),
  clearOrdersBtn: document.getElementById('clear-orders'),
  resetMenuBtn: document.getElementById('reset-menu'),
  logoutBtn: document.getElementById('admin-logout'),
  toSiteBtns: document.querySelectorAll('#admin-to-site, #admin-open-site'),
};

function showAdminRoot() {
  document.getElementById('site-root').style.display = 'none';
  admin.root.style.display = 'block';
  // if already logged in, show admin UI
  if (isAdminLoggedIn()) {
    showAdminUI();
  } else {
    showAdminLogin();
  }
  bindAdminEvents();
}

function showAdminLogin() {
  admin.loginBox.style.display = 'block';
  admin.adminUI.style.display = 'none';
  admin.adminUI.querySelectorAll('*').forEach(el => {}); // noop
  admin.loginMsg.style.display = 'none';
}

function showAdminUI() {
  admin.loginBox.style.display = 'none';
  admin.adminUI.style.display = 'block';
  admin.adminUI.querySelector('#admin-ui')?.classList; // noop
  renderAdminMenu();
  renderOrders();
}

function bindAdminEvents() {
  // login
  admin.loginBtn.onclick = () => {
    const u = admin.userInput.value.trim();
    const p = admin.passInput.value.trim();
    // DEMO creds
    if (u === 'admin' && p === 'admin123') {
      setAdminLoggedIn(true);
      admin.loginMsg.style.display = 'none';
      showAdminUI();
    } else {
      admin.loginMsg.style.display = 'block';
      admin.loginMsg.textContent = 'Invalid credentials (demo: admin / admin123)';
    }
  };
  // logout
  admin.logoutBtn.onclick = () => {
    setAdminLoggedIn(false);
    admin.userInput.value = '';
    admin.passInput.value = '';
    showAdminLogin();
  };
  // open public site
  admin.toSiteBtns.forEach(b => b.onclick = () => {
    // navigate to public site root
    // if launching from hash route, remove hash; otherwise navigate to /
    if (window.location.hash === '#/admin') {
      window.location.hash = '#/home';
    } else if (window.location.pathname.includes('/admin')) {
      // try to navigate to parent (best effort)
      const p = window.location.pathname.replace(/\\/admin\\/?.*$/, '/');
      try { window.location.pathname = p; } catch(e) { window.location.href = '/'; }
    } else {
      window.location.href = window.location.pathname || './';
    }
  });

  // add menu item
  admin.addMenuBtn.onclick = () => {
    const name = admin.newNameInput.value.trim();
    const price = parseFloat(admin.newPriceInput.value);
    const img = admin.newImgInput.value.trim();
    if (!name || !price || isNaN(price)) {
      alert('Please provide valid name and price.');
      return;
    }
    const menu = loadMenu();
    menu.push({ name, price, img: img || generatePlaceholderImg(name) });
    saveMenu(menu);
    menuData = menu;
    renderAdminMenu();
    // reflect changes on public site if active
    renderMenuCards(menuData);
    admin.newNameInput.value = '';
    admin.newPriceInput.value = '';
    admin.newImgInput.value = '';
  };

  // reset menu to default
  admin.resetMenuBtn.onclick = () => {
    if (!confirm('Reset menu to default items?')) return;
    saveMenu(DEFAULT_MENU);
    menuData = loadMenu();
    renderAdminMenu();
    renderMenuCards(menuData);
  };

  // clear orders
  admin.clearOrdersBtn.onclick = () => {
    if (!confirm('Clear all orders?')) return;
    saveOrders([]);
    renderOrders();
  };
}

/* Admin: render menu list with delete */
function renderAdminMenu() {
  const menu = loadMenu();
  admin.menuListRoot.innerHTML = '';
  menu.forEach((m, idx) => {
    const row = document.createElement('div');
    row.style.display = 'flex';
    row.style.justifyContent = 'space-between';
    row.style.alignItems = 'center';
    row.style.padding = '8px 6px';
    row.style.borderBottom = '1px solid #f5f5f5';
    row.innerHTML = `
      <div style="display:flex; gap:10px; align-items:center;">
        <img src="${m.img || ''}" style="width:52px;height:40px;object-fit:cover;border-radius:8px;border:1px solid #eee" />
        <div>
          <div style="font-weight:700;">${escapeHtml(m.name)}</div>
          <div style="font-size:13px;color:#666">₹${m.price}</div>
        </div>
      </div>
      <div style="display:flex;gap:8px;">
        <button class="danger" data-idx="${idx}" style="padding:6px 10px;">Delete</button>
      </div>
    `;
    admin.menuListRoot.appendChild(row);
  });
  // attach deletes
  admin.menuListRoot.querySelectorAll('button[data-idx]').forEach(btn => {
    btn.onclick = (e) => {
      const idx = Number(btn.getAttribute('data-idx'));
      if (!confirm('Delete this menu item?')) return;
      const menu = loadMenu();
      menu.splice(idx, 1);
      saveMenu(menu);
      menuData = menu;
      renderAdminMenu();
      renderMenuCards(menuData);
    };
  });
}

/* Admin: render orders list */
function renderOrders() {
  const orders = loadOrders();
  admin.ordersListRoot.innerHTML = '';
  if (!orders || orders.length === 0) {
    admin.ordersListRoot.innerHTML = '<div style="color:#666;">No orders yet.</div>';
    return;
  }
  orders.forEach(order => {
    const div = document.createElement('div');
    div.className = 'order-card';
    const date = new Date(order.createdAt).toLocaleString();
    div.innerHTML = `
      <div style="display:flex; justify-content:space-between; align-items:center;">
        <div style="font-weight:700;">${order.id}</div>
        <div style="font-size:13px;color:#666">${date}</div>
      </div>
      <div style="margin-top:6px;">${order.items.map(it=>`${escapeHtml(it.name)} x ${it.qty}`).join(', ')}</div>
      <div style="margin-top:8px; display:flex; justify-content:space-between; align-items:center;">
        <div style="font-weight:800;">Total: ₹${order.total}</div>
        <div style="font-size:13px;color:#666">${order.status}</div>
      </div>
    `;
    admin.ordersListRoot.appendChild(div);
  });
}

/* =========================
   Helpers & utilities
   ========================= */

function escapeHtml(str) {
  if (!str) return '';
  return String(str).replace(/[&<>"']/g, s => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[s]));
}
function escapeJs(str) {
  return (str||'').replace(/'/g,"\\'");
}
function generatePlaceholderImg(text) {
  return 'https://dummyimage.com/600x400/eee/aaa&text=' + encodeURIComponent(text);
}

/* =========================
   Initialize whole app
   ========================= */

(function initApp(){
  // check if admin route else public
  routeInit();

  // Also update menuData from storage on load
  menuData = loadMenu();

  // On hashchange or popstate, re-route
  window.addEventListener('hashchange', routeInit);
  window.addEventListener('popstate', routeInit);

  // If user returns from admin -> update public UI accordingly
  // Allow top nav cart bubble click to scroll to menu
  document.getElementById('top-nav').addEventListener('click', (e) => {
    if (e.target.tagName === 'A') {
      const href = e.target.getAttribute('href');
      if (href === '#menu') {
        location.hash = '#menu';
        setTimeout(()=>document.getElementById('menu-grid').scrollIntoView({behavior:'smooth'}), 100);
      }
    }
  });
})();
</script>
</body>
</html>
