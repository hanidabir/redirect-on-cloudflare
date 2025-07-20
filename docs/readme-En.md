# 🌐 Redirecting a Domain to Another Domain Using Cloudflare

If you want to redirect all traffic from one domain to another — including path and query parameters — Cloudflare provides two effective methods: **Redirect Rules** (recommended) and **Page Rules** (legacy).

---

## 1. Add Your Domain to Cloudflare

* Log into your Cloudflare dashboard.
* Add your source domain (the one you want to redirect from) if you haven’t already.
* Ensure Cloudflare is **active** on this domain (check that the nameservers have been updated and DNS is proxied).

---

## 2. Create Proxied DNS Records

Cloudflare needs proxied (orange-cloud) records to apply redirect rules. You can add dummy records for this purpose:

* Go to the **DNS** tab of your domain.
* Add the following **A records** with a placeholder IP (e.g., `192.0.2.1` – reserved for documentation use):

  | Name | Type | Content   | Proxy Status |
  | ---- | ---- | --------- | ------------ |
  | @    | A    | 192.0.2.1 | Proxied 🔶   |
  | www  | A    | 192.0.2.1 | Proxied 🔶   |

> These records do not serve actual content. Cloudflare handles the redirect before reaching any origin server.

---

## 3. Choose a Redirect Method

Cloudflare offers two redirect options:

### 🔸 Option 1: **Redirect Rules** (Recommended)

* Go to the **Rules** section in the left sidebar.
* Select **Redirect Rules**.
* Click **Create Rule** → **Redirect Rule**.

### 🔹 Option 2: **Page Rules** (Legacy)

* If your plan doesn’t support Redirect Rules, use Page Rules:
* Go to **Rules** → **Page Rules**.
* Click **Create Page Rule**.

> ⚠️ Note: Cloudflare recommends using Redirect Rules for new setups.

---

## 4. Configure the Redirect

### 🔸 Using **Redirect Rules**

* **Rule Name:** e.g., `Full Domain Redirect`
* **Condition:**

  * **Field:** `Hostname`
  * **Operator:** `equals`
  * **Value:** `yourdomain.com`
  * *(Add another condition for `www.yourdomain.com` if needed)*
* **Action:**

  * **Type:** `Static Redirect`
  * **Status Code:** `301 - Permanent Redirect` (or `302` if temporary)
  * **Target URL:**

    ```
    https://newdomain.com/{path}?{query}
    ```

> This ensures `yourdomain.com/page?x=1` becomes `newdomain.com/page?x=1`.

---

### 🔹 Using **Page Rules**

* **URL Pattern:**

  ```
  *yourdomain.com/*
  ```

  *(This covers both root and www if your DNS is set correctly)*
* **Setting:** `Forwarding URL`
* **Status Code:** `301 - Permanent Redirect`
* **Destination URL:**

  ```
  https://newdomain.com/$1
  ```

> `$1` matches the wildcard portion after the slash — e.g., `/about` → `/about`

---

## 5. Deploy and Test

* Click **Deploy Rule** or **Save and Deploy Page Rule**.
* Open a private/incognito browser window.
* Try visiting:

  * `http://yourdomain.com/`
  * `http://yourdomain.com/some-page?query=test`
* You should be redirected to:

  * `https://newdomain.com/`
  * `https://newdomain.com/some-page?query=test`

> 🔍 Pro Tip: Use online tools like [Redirect Checker](https://httpstatus.io) to verify headers and response codes.

---

## ✅ Summary

| Step | Description                                |
| ---- | ------------------------------------------ |
| 1    | Add domain to Cloudflare                   |
| 2    | Create proxied A records                   |
| 3    | Choose Redirect Rules (or Page Rules)      |
| 4    | Set up the redirect with proper conditions |
| 5    | Deploy and test the behavior               |

---
