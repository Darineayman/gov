# Government Service Portal — Custom Widgets

A complete, paste-ready set of ServiceNow Service Portal widgets, styled to match your design system and wired to your tables (`u_citizen_profile`, `u_government_service`, `u_service_request`, `u_appointment`, …).

---

## How to use this document

Each widget below has up to **five parts**. In ServiceNow open **Service Portal → Service Portal Configuration → Widget Editor**, create a new widget, and paste each part into its matching field:

| Section in this doc | Widget Editor field |
|---|---|
| `HTML Template` | **Body HTML template** |
| `CSS / SCSS`    | **CSS - SCSS** |
| `Client Script` | **Client controller** |
| `Server Script` | **Server script** |
| `Option Schema` | (gear icon) → **Edit option schema** |

> **Theme / fonts.** Add the Google Fonts link once at the portal level: **Service Portal → Portals → (your portal) → Theme → CSS Variables / JS includes**, or drop a single global CSS asset. The widgets below assume `Fraunces` and `Spline Sans` are loaded. If they aren't, the widgets still work — they just fall back to system fonts.

---

## 0. Shared theme variables (paste once into your Portal Theme → CSS)

```scss
:root {
  --paper:#F7F6F1; --ink:#1A1B16; --ink-soft:#54564C; --ink-faint:#86887C;
  --line:#E3E1D6; --line-soft:#EEEDE4; --card:#FFFFFF;
  --green:#5E7E1C; --green-bright:#86BC25; --green-deep:#3B6D11; --green-wash:#F1F4E6;
  --blue:#185FA5; --blue-wash:#E9F0F6; --blue-ink:#2C4F6E;
  --amber:#854F0B; --amber-wash:#FBF1DD; --amber-ink:#7A5410;
  --teal:#0F6E56; --teal-wash:#E1F5EE;
  --red:#9A2D1F; --red-wash:#F8E7E3;
  --gp-radius:14px;
  --gp-serif:'Fraunces',Georgia,serif;
  --gp-sans:'Spline Sans',system-ui,sans-serif;
}
```

In the portal theme’s **JS Includes** or a UI page header, load fonts:
```html
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,400;9..144,600&family=Spline+Sans:wght@400;500;600&display=swap" rel="stylesheet">
```

---

# WIDGET 1 — `gov_login`  (Citizen Sign-in)

Styled sign-in form. It submits to ServiceNow’s **real** login servlet, so it actually authenticates. On a wrong password the platform redirects back with `?error`, which we detect and show.

Put this widget on a page you’ll register as your **portal login page** (Portal record → *Login page* field).

### HTML Template
```html
<div class="gp-auth-wrap">
  <div class="gp-auth-card">
    <div class="gp-brand"><span class="gp-dot"></span>
      <span class="gp-brand-name">Government Service Portal</span>
    </div>

    <h1 class="gp-title">Welcome back</h1>
    <p class="gp-sub">Sign in to your citizen account to manage services and requests.</p>

    <div class="gp-alert gp-alert-error" ng-if="c.loginFailed">
      Incorrect email or password. Please try again.
    </div>
    <div class="gp-alert gp-alert-success" ng-if="c.justRegistered">
      Account created — you can now sign in.
    </div>

    <form name="loginForm" action="/login.do" method="post">
      <input type="hidden" name="sysparm_ck" value="{{c.data.ck}}"/>
      <input type="hidden" name="sys_action" value="sysverb_login"/>

      <div class="gp-group">
        <label class="gp-label">Email address</label>
        <input class="gp-input" type="text" name="user_name"
               placeholder="name@example.com" autocomplete="username" required>
      </div>

      <div class="gp-group">
        <label class="gp-label">Password</label>
        <div class="gp-input-wrap">
          <input class="gp-input" name="user_password"
                 type="{{c.showPw ? 'text' : 'password'}}"
                 placeholder="Your password" autocomplete="current-password" required>
          <button class="gp-eye" type="button" ng-click="c.showPw=!c.showPw"
                  aria-label="Toggle password">
            <i class="fa" ng-class="c.showPw ? 'fa-eye-slash' : 'fa-eye'"></i>
          </button>
        </div>
      </div>

      <button class="gp-btn gp-btn-primary" type="submit">Sign in</button>
    </form>

    <p class="gp-switch">
      Don’t have an account?
      <a class="gp-link" href="?id={{c.options.register_page || 'gov_register'}}">Create one</a>
    </p>
  </div>
</div>
```

### CSS / SCSS
```scss
.gp-auth-wrap{min-height:78vh;display:flex;align-items:center;justify-content:center;padding:40px 16px;font-family:var(--gp-sans)}
.gp-auth-card{background:var(--card);border:1px solid var(--line);border-radius:20px;padding:44px 40px;width:100%;max-width:440px}
.gp-brand{display:flex;align-items:center;gap:10px;margin-bottom:30px}
.gp-dot{width:10px;height:10px;border-radius:50%;background:var(--green-bright)}
.gp-brand-name{font-family:var(--gp-serif);font-weight:600;font-size:16px;color:var(--ink)}
.gp-title{font-family:var(--gp-serif);font-weight:600;font-size:26px;letter-spacing:-.02em;margin:0 0 6px;color:var(--ink)}
.gp-sub{font-size:14px;color:var(--ink-soft);margin:0 0 28px;line-height:1.5}
.gp-group{margin-bottom:18px}
.gp-label{display:block;font-size:12.5px;font-weight:500;color:var(--ink-soft);margin-bottom:7px}
.gp-input{width:100%;padding:11px 15px;border:1px solid var(--line);border-radius:10px;font-size:15px;font-family:var(--gp-sans);background:var(--paper);color:var(--ink);outline:none;transition:border-color .15s,box-shadow .15s}
.gp-input:focus{border-color:var(--green-bright);box-shadow:0 0 0 3px rgba(134,188,37,.15);background:var(--card)}
.gp-input-wrap{position:relative}
.gp-input-wrap .gp-input{padding-right:44px}
.gp-eye{position:absolute;right:12px;top:50%;transform:translateY(-50%);background:none;border:none;cursor:pointer;color:var(--ink-faint)}
.gp-btn{width:100%;padding:13px;border-radius:10px;font-size:15px;font-weight:600;font-family:var(--gp-sans);cursor:pointer;border:none;margin-top:4px;transition:opacity .15s}
.gp-btn-primary{background:var(--green-bright);color:#fff}
.gp-btn-primary:hover{opacity:.9}
.gp-switch{text-align:center;font-size:13.5px;color:var(--ink-soft);margin-top:22px}
.gp-link{color:var(--green);font-weight:500;cursor:pointer}
.gp-link:hover{text-decoration:underline}
.gp-alert{padding:12px 15px;border-radius:10px;font-size:13.5px;margin-bottom:20px;border:1px solid}
.gp-alert-error{background:var(--red-wash);border-color:#F5C0BA;color:var(--red)}
.gp-alert-success{background:var(--green-wash);border-color:#D6E3B8;color:var(--green-deep)}
```

### Client Script
```javascript
function($scope, $location) {
  var c = this;
  c.showPw = false;

  // Detect the platform's failed-login redirect (…/login_page?...&error or sysparm_login_url)
  var search = $location.search();
  c.loginFailed   = !!(search.error || search.login_failed);
  c.justRegistered = !!search.registered;
}
```

### Server Script
```javascript
(function() {
  // The login form needs a valid session token to post to /login.do
  data.ck = gs.getSessionToken();
})();
```

### Option Schema
```json
[
  { "name": "register_page", "label": "Register page ID", "type": "string", "default": "gov_register" }
]
```

> **Wire-up:** On your **Portal record** set *Login page* to the page that hosts this widget. ServiceNow will redirect unauthenticated users here automatically, and after a successful login it sends them to the portal’s *Homepage* (set that to your `gov_home` page).

---

# WIDGET 2 — `gov_register`  (Citizen Registration)

Creates a real `sys_user` **and** a linked `u_citizen_profile` record, then bounces to the login page with `?registered=1`. Live password-strength meter and confirm-match, matching the design.

### HTML Template
```html
<div class="gp-auth-wrap">
  <div class="gp-auth-card">
    <div class="gp-brand"><span class="gp-dot"></span>
      <span class="gp-brand-name">Government Service Portal</span>
    </div>

    <h1 class="gp-title">Create account</h1>
    <p class="gp-sub">Register to access government services online from anywhere.</p>

    <div class="gp-alert gp-alert-error" ng-if="c.error">{{c.error}}</div>

    <div class="gp-row">
      <div class="gp-group">
        <label class="gp-label">First name</label>
        <input class="gp-input" ng-model="c.f.first" type="text" placeholder="Ahmed">
      </div>
      <div class="gp-group">
        <label class="gp-label">Last name</label>
        <input class="gp-input" ng-model="c.f.last" type="text" placeholder="Hassan">
      </div>
    </div>

    <div class="gp-group">
      <label class="gp-label">National ID</label>
      <input class="gp-input" ng-model="c.f.nid" type="text" placeholder="14-digit national ID">
    </div>

    <div class="gp-group">
      <label class="gp-label">Mobile</label>
      <input class="gp-input" ng-model="c.f.mobile" type="text" placeholder="+20 1XX XXX XXXX">
    </div>

    <div class="gp-group">
      <label class="gp-label">Email address</label>
      <input class="gp-input" ng-model="c.f.email" type="email" placeholder="name@example.com">
    </div>

    <div class="gp-group">
      <label class="gp-label">Password</label>
      <div class="gp-input-wrap">
        <input class="gp-input" ng-model="c.f.pw" ng-change="c.scorePw()"
               type="{{c.showPw?'text':'password'}}" placeholder="Min. 8 characters">
        <button class="gp-eye" type="button" ng-click="c.showPw=!c.showPw" aria-label="Toggle">
          <i class="fa" ng-class="c.showPw?'fa-eye-slash':'fa-eye'"></i>
        </button>
      </div>
      <div class="gp-pwbar"><div class="gp-pwfill" ng-style="c.pwStyle"></div></div>
    </div>

    <div class="gp-group">
      <label class="gp-label">Confirm password</label>
      <input class="gp-input" ng-model="c.f.confirm" type="password" placeholder="Repeat password">
    </div>

    <button class="gp-btn gp-btn-primary" ng-click="c.submit()" ng-disabled="c.busy">
      <span ng-if="!c.busy">Create account</span>
      <span ng-if="c.busy">Creating account…</span>
    </button>

    <p class="gp-switch">
      Already have an account?
      <a class="gp-link" href="?id={{c.options.login_page || 'gov_login'}}">Sign in</a>
    </p>
  </div>
</div>
```

### CSS / SCSS
```scss
/* reuses gov_login styles; add only the extras */
.gp-row{display:grid;grid-template-columns:1fr 1fr;gap:14px}
.gp-pwbar{height:4px;border-radius:4px;background:var(--line);margin-top:8px;overflow:hidden}
.gp-pwfill{height:100%;border-radius:4px;transition:width .3s,background .3s;width:0}
.gp-btn-primary[disabled]{opacity:.55;cursor:not-allowed}
```
*(If `gov_register` is on a different page than `gov_login`, copy the full `gov_login` CSS block in here too so the shared classes resolve.)*

### Client Script
```javascript
function($scope, $window) {
  var c = this;
  c.f = {}; c.showPw = false; c.busy = false;
  c.pwStyle = { width: '0%', background: '#D0402E' };

  c.scorePw = function() {
    var p = c.f.pw || '', s = 0;
    if (p.length >= 8) s++;
    if (/[A-Z]/.test(p)) s++;
    if (/[0-9]/.test(p)) s++;
    if (/[^A-Za-z0-9]/.test(p)) s++;
    var colors = ['#D0402E', '#EF9F27', '#86BC25', '#5E7E1C'];
    c.pwStyle = { width: (s * 25) + '%', background: colors[Math.max(0, s - 1)] };
  };

  c.submit = function() {
    c.error = '';
    var f = c.f;
    if (!f.first || !f.last)      { c.error = 'First and last name are required.'; return; }
    if (!f.email || !/\S+@\S+\.\S+/.test(f.email)) { c.error = 'A valid email is required.'; return; }
    if (!f.nid)                   { c.error = 'National ID is required.'; return; }
    if ((f.pw || '').length < 8)  { c.error = 'Password must be at least 8 characters.'; return; }
    if (f.pw !== f.confirm)       { c.error = 'Passwords do not match.'; return; }

    c.busy = true;
    c.data.action = 'register';
    c.data.form = f;
    c.server.update().then(function() {
      c.busy = false;
      if (c.data.ok) {
        var lp = c.options.login_page || 'gov_login';
        $window.location.href = '?id=' + lp + '&registered=1';
      } else {
        c.error = c.data.error || 'Registration failed. Please try again.';
      }
      c.data.action = ''; // reset
    });
  };
}
```

### Server Script
```javascript
(function() {
  data.ok = false;

  if (input && input.action === 'register') {
    var f = input.form || {};

    // 1. Reject duplicate email
    var dup = new GlideRecord('sys_user');
    dup.addQuery('email', f.email);
    dup.query();
    if (dup.next()) { data.error = 'An account with this email already exists.'; return; }

    // 2. Create the platform user
    var u = new GlideRecord('sys_user');
    u.initialize();
    u.first_name = f.first;
    u.last_name  = f.last;
    u.email      = f.email;
    u.user_name  = f.email;          // login with email
    u.user_password = f.pw;          // ServiceNow hashes on save
    u.mobile_phone  = f.mobile || '';
    var userId = u.insert();

    if (!userId) { data.error = 'Could not create the user account.'; return; }

    // 3. Grant the citizen role
    var r = new GlideRecord('sys_user_role');
    r.addQuery('name', 'x_citizen'); // <-- use your real role name (e.g. u_citizen / x_<scope>_citizen)
    r.query();
    if (r.next()) {
      var has = new GlideRecord('sys_user_has_role');
      has.initialize();
      has.user = userId;
      has.role = r.getUniqueValue();
      has.insert();
    }

    // 4. Create the citizen profile (your table)
    var p = new GlideRecord('u_citizen_profile');
    p.initialize();
    p.u_user_account = userId;       // reference to sys_user
    p.u_first_name   = f.first;
    p.u_last_name    = f.last;
    p.u_national_id  = f.nid;
    p.u_email        = f.email;
    p.u_mobile       = f.mobile || '';
    p.u_verification_status = 'pending';
    p.u_active = true;
    p.insert();

    data.ok = true;
  }
})();
```

### Option Schema
```json
[
  { "name": "login_page", "label": "Login page ID", "type": "string", "default": "gov_login" }
]
```

> ⚠️ **Permissions for registration.** A registration page is **public** (no login yet), so the widget runs as the *guest* user, who normally can’t insert `sys_user`. Two reliable options:
> 1. **Recommended:** put the insert logic in a **Script Include** marked *client-callable* and call it through a **public Scripted REST resource** that runs with elevated rights — keeps creation server-side and safe.
> 2. **Quick path:** give the `public`/`snc_external` user *create* ACLs on `sys_user`, `sys_user_has_role`, and `u_citizen_profile` (lock these down tightly — only the fields above).
>
> Also map your real field names: I used `u_user_account`, `u_first_name`, etc. Adjust to whatever your dictionary actually shows.

---

# WIDGET 3 — `gov_home`  (Citizen Dashboard)

The post-login landing page. Greets the user, shows live counts pulled from `u_service_request` / `u_appointment`, and renders the service catalogue from `u_government_service`. Each card links to a request page, passing the service sys_id.

### HTML Template
```html
<div class="gp-home">
  <div class="gp-topbar">
    <div class="gp-brand"><span class="gp-dot"></span>
      <span class="gp-brand-name">Government Service Portal</span>
    </div>
    <div class="gp-userchip">
      <div class="gp-avatar">{{c.data.initials}}</div>
      <span class="gp-username">{{c.data.fullName}}</span>
      <a class="gp-logout" href="/logout.do">Sign out</a>
    </div>
  </div>

  <div class="gp-body">
    <div class="gp-banner">
      <div class="gp-banner-icon"><i class="fa fa-home"></i></div>
      <div>
        <h2 class="gp-banner-title">Welcome, {{c.data.firstName}}</h2>
        <p class="gp-banner-sub">Submit requests, track their status, book appointments and manage documents — all from here.</p>
      </div>
    </div>

    <div class="gp-seclabel">Your status</div>
    <div class="gp-stats">
      <div class="gp-stat"><div class="gp-stat-num" style="color:var(--green)">{{c.data.stats.active}}</div><div class="gp-stat-lbl">Active requests</div></div>
      <div class="gp-stat"><div class="gp-stat-num" style="color:var(--blue)">{{c.data.stats.pending}}</div><div class="gp-stat-lbl">Pending review</div></div>
      <div class="gp-stat"><div class="gp-stat-num" style="color:var(--amber-ink)">{{c.data.stats.appts}}</div><div class="gp-stat-lbl">Upcoming appointments</div></div>
      <div class="gp-stat"><div class="gp-stat-num" style="color:var(--green-deep)">{{c.data.stats.done}}</div><div class="gp-stat-lbl">Completed</div></div>
    </div>

    <div class="gp-seclabel">Available services</div>
    <div class="gp-services">
      <a class="gp-service" ng-repeat="s in c.data.services"
         href="?id={{c.options.request_page||'gov_request'}}&service={{s.sys_id}}">
        <div class="gp-sc-icon" ng-style="{background:s.bg}">
          <i class="fa" ng-class="s.icon" ng-style="{color:s.fg}"></i>
        </div>
        <div class="gp-sc-title">{{s.name}}</div>
        <div class="gp-sc-desc">{{s.category}} · SLA {{s.sla}} day(s)</div>
      </a>
    </div>
  </div>
</div>
```

### CSS / SCSS
```scss
.gp-home{font-family:var(--gp-sans);color:var(--ink)}
.gp-topbar{background:var(--card);border-bottom:1px solid var(--line);padding:0 32px;display:flex;align-items:center;justify-content:space-between;height:60px}
.gp-userchip{display:flex;align-items:center;gap:10px}
.gp-avatar{width:34px;height:34px;border-radius:50%;background:var(--green-wash);border:1px solid #D6E3B8;display:flex;align-items:center;justify-content:center;font-size:13px;font-weight:600;color:var(--green-deep)}
.gp-username{font-size:14px;font-weight:500}
.gp-logout{font-size:13px;color:var(--ink-faint);border:1px solid var(--line);padding:5px 14px;border-radius:8px;text-decoration:none}
.gp-logout:hover{background:var(--paper)}
.gp-body{padding:40px 32px;max-width:960px;margin:0 auto}
.gp-banner{background:var(--card);border:1px solid var(--line);border-radius:var(--gp-radius);padding:28px 32px;margin-bottom:28px;display:flex;align-items:center;gap:20px}
.gp-banner-icon{width:52px;height:52px;border-radius:14px;background:var(--green-wash);border:1px solid #D6E3B8;display:flex;align-items:center;justify-content:center;color:var(--green-deep);font-size:22px}
.gp-banner-title{font-family:var(--gp-serif);font-size:22px;font-weight:600;margin:0 0 4px}
.gp-banner-sub{font-size:14px;color:var(--ink-soft);margin:0;line-height:1.5}
.gp-seclabel{font-size:11px;letter-spacing:.1em;text-transform:uppercase;color:var(--ink-faint);font-weight:500;margin-bottom:14px}
.gp-stats{display:grid;grid-template-columns:repeat(auto-fit,minmax(140px,1fr));gap:12px;margin-bottom:32px}
.gp-stat{background:var(--card);border:1px solid var(--line);border-radius:var(--gp-radius);padding:18px 20px}
.gp-stat-num{font-family:var(--gp-serif);font-size:30px;font-weight:600;line-height:1;margin-bottom:4px}
.gp-stat-lbl{font-size:12px;color:var(--ink-faint)}
.gp-services{display:grid;grid-template-columns:repeat(auto-fit,minmax(190px,1fr));gap:14px}
.gp-service{display:block;background:var(--card);border:1px solid var(--line);border-radius:var(--gp-radius);padding:20px;text-decoration:none;color:inherit;transition:border-color .15s,box-shadow .15s}
.gp-service:hover{border-color:var(--green-bright);box-shadow:0 2px 16px rgba(94,126,28,.08)}
.gp-sc-icon{width:38px;height:38px;border-radius:10px;display:flex;align-items:center;justify-content:center;margin-bottom:12px;font-size:18px}
.gp-sc-title{font-size:14px;font-weight:500;margin-bottom:3px}
.gp-sc-desc{font-size:12.5px;color:var(--ink-faint)}
```

### Client Script
```javascript
function() {
  var c = this;
  // Everything comes from the server; nothing to do client-side on load.
}
```

### Server Script
```javascript
(function() {
  var uid = gs.getUserID();
  var user = gs.getUser();
  data.firstName = user.getFirstName() || user.getName();
  data.fullName  = user.getFullName();
  data.initials  = ((user.getFirstName() || ' ')[0] + (user.getLastName() || ' ')[0]).toUpperCase();

  // Find this citizen's profile to scope their requests
  var profileId = '';
  var prof = new GlideRecord('u_citizen_profile');
  prof.addQuery('u_user_account', uid);
  prof.query();
  if (prof.next()) profileId = prof.getUniqueValue();

  // --- Live stats from u_service_request ---
  data.stats = { active: 0, pending: 0, done: 0, appts: 0 };

  function count(tbl, build) {
    var gr = new GlideRecord(tbl);
    build(gr);
    gr.query();
    return gr.getRowCount();
  }

  if (profileId) {
    data.stats.active  = count('u_service_request', function(g){ g.addQuery('u_citizen', profileId); g.addQuery('u_request_status', 'pending'); });
    data.stats.pending = count('u_service_request', function(g){ g.addQuery('u_citizen', profileId); g.addQuery('u_current_stage', 'verification'); });
    data.stats.done    = count('u_service_request', function(g){ g.addQuery('u_citizen', profileId); g.addQuery('u_request_status', 'approved'); });
    data.stats.appts   = count('u_appointment',     function(g){ g.addQuery('u_citizen', profileId); g.addQuery('u_status', 'scheduled'); });
  }

  // --- Service catalogue from u_government_service (active only) ---
  // icon/colour mapping by category, matching the design palette
  var palette = {
    'Licensing':  { icon:'fa-id-card',   bg:'#F1F4E6', fg:'#3B6D11' },
    'Civil':      { icon:'fa-file-text', bg:'#E9F0F6', fg:'#2C4F6E' },
    'Complaints': { icon:'fa-exclamation-circle', bg:'#FBF1DD', fg:'#7A5410' },
    'Utilities':  { icon:'fa-bolt',      bg:'#E1F5EE', fg:'#0F6E56' },
    'Permits':    { icon:'fa-building',  bg:'#F1F4E6', fg:'#3B6D11' },
    '_default':   { icon:'fa-folder',    bg:'#E9F0F6', fg:'#2C4F6E' }
  };

  data.services = [];
  var sv = new GlideRecord('u_government_service');
  sv.addQuery('u_active', true);
  sv.orderBy('u_service_name');
  sv.query();
  while (sv.next()) {
    var cat = sv.getValue('u_category') || '';
    var look = palette[cat] || palette._default;
    data.services.push({
      sys_id:   sv.getUniqueValue(),
      name:     sv.getValue('u_service_name'),
      category: cat,
      sla:      sv.getValue('u_sla_target_days') || '—',
      icon:     look.icon,
      bg:       look.bg,
      fg:       look.fg
    });
  }
})();
```

### Option Schema
```json
[
  { "name": "request_page", "label": "Request page ID", "type": "string", "default": "gov_request" }
]
```

---

## Field-name cheat sheet (adjust to your dictionary)

I assumed `u_`-prefixed names from your data model. Verify each against the actual column names in **System Definition → Tables**:

| Used in code | Your table.field |
|---|---|
| `u_citizen_profile.u_user_account` | reference → `sys_user` |
| `u_citizen_profile.u_national_id`  | National ID (string) |
| `u_citizen_profile.u_verification_status` | choice: pending/verified/rejected |
| `u_service_request.u_citizen`      | reference → `u_citizen_profile` |
| `u_service_request.u_current_stage`| choice: submitted/payment/verification/approval/completed |
| `u_service_request.u_request_status`| choice: pending/approved/rejected |
| `u_government_service.u_active`    | true/false |
| `u_government_service.u_category`  | choice (matches palette keys) |
| `u_appointment.u_status`           | choice: scheduled/completed/cancelled |

If your choice **values** differ from the **labels** (e.g. value `1` shows as “Pending”), query by the stored *value*, not the label.

---

## Page & portal wiring (do this once)

1. **Create three Service Portal pages**: `gov_login`, `gov_register`, `gov_home`. Drop the matching widget on each.
2. **Portal record** (`Service Portal → Portals`):
   - *Login page* → `gov_login`
   - *Homepage* → `gov_home`
3. **Public pages**: open the `gov_login` and `gov_register` **page records** and tick **Public** (so unauthenticated visitors can reach them).
4. Build the next page, `gov_request`, to receive the `?service=<sys_id>` parameter and create a `u_service_request`. That’s the natural next widget.

---

## What to build next (same pattern)

- **`gov_request`** — service detail + submit form → inserts `u_service_request`, pulls dept/SLA/fee/flags from the chosen `u_government_service`.
- **`gov_my_requests`** — list + status timeline of the citizen’s requests.
- **`gov_documents`** — upload widget writing `u_document` rows (per-file approve/reject).
- **`gov_appointments`** — slot picker reading `u_appointment_slot` (Active && Booked < Capacity) and inserting `u_appointment`.
- **Officer/admin** — separate pages gated by role for `support_officer`, `verification_officer`, `manager`, `gov_admin`.

Tell me which one and I’ll write it in the same style.
