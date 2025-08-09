

# 🌐 Domain Setup & DNS Configuration

**Objective:**  
Configure the domain `sudheerbhat.in` using GoDaddy DNS settings to point to an AWS EC2 instance, enabling website access via sudheerbhat.in 

## Step1 Purchase the Domain
- Domain purchased from **GoDaddy**: `sudheerbhat.in`

---

## Step2 Configure the A Record

| Type | Name | Value          | TTL   |
|------|------|----------------|-------|
| A    | @    | `<Public IP>`  | 600s  |

**Explanation**
- **Type:** Maps the domain to an IPv4 address.
- **Name:** `@` → Root domain (`sudheerbhat.in`)
- **Value:** Public IP of the EC2 instance.
- **TTL:** Time (in seconds) DNS resolvers cache this record — `600s` = 10 mins.

✅ **Effect:** Routes `sudheerbhat.in` → EC2 server.

> ⚠ **Note:** Restarting EC2 changes the public IP (unless using an Elastic IP). Update this record if IP changes.

---

## Step3 Keep GoDaddy as DNS Manager
- **Do not change Name Servers** for now.
- GoDaddy continues managing DNS.

---

## Step4 Configure the CNAME Record

| Type  | Name | Value           | TTL     |
|-------|------|-----------------|---------|
| CNAME | www  | sudheerbhat.in  | 1 Hour  |

**Explanation**
- **Type:** Alias to another domain.
- **Name:** `www` → Subdomain (`www.sudheerbhat.in`)
- **Value:** Points to `sudheerbhat.in`
- **TTL:** DNS cache time = 1 hour.

✅ **Effect:** Routes `www.sudheerbhat.in` → `sudheerbhat.in`.

---

## Step5 Save Records
- Click **Save** in GoDaddy DNS settings after updating.

---

## Step6 Test the Domain
1. Open `https://sudheerbhat.in` in a browser.
2. If not loading:
   - Wait for DNS propagation (few mins to hours).
   - Check A record IP is correct.
3. Use [DNS Checker](https://dnschecker.org/) to verify updates.

---

