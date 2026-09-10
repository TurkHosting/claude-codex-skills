# Customer reply templates

Placeholders: `{IP}`, `{REQUESTED_PTR}`, `{BRAND_DOMAIN}`.
The English "Third-party domain" text below is the operator's approved wording —
use it as-is.

---

## 1. Third-party / trademark domain — EN (default)

```
Dear Customer,

Thank you for reaching out to us.

We have received your request to update the PTR (Reverse DNS) record for the IP address {IP} to {REQUESTED_PTR}.

Please be informed that {BRAND_DOMAIN} is a highly protected, globally recognized trademark. To ensure network security and comply with international anti-spoofing and anti-spam regulations, we cannot map a PTR record to a domain owned by a third-party corporation without explicit authorization.

In order to proceed with this update, could you please provide one of the following?

Official Authorization: Documentation or written consent from the official domain administrator of {BRAND_DOMAIN} authorizing the use of this subdomain on your server.

Alternative Domain: If you do not have official authorization, please provide an alternative domain name that you own or legally control to be set as the PTR record.

Please note that we will hold this request until the necessary verification or an alternative domain is provided. Thank you for your cooperation and understanding in maintaining a secure hosting environment.

Best regards
```

## 1a. Third-party / trademark domain — TR

```
Sayın Müşterimiz,

Bize ulaştığınız için teşekkür ederiz.

{IP} IP adresinin PTR (Ters DNS) kaydının {REQUESTED_PTR} olarak güncellenmesi yönündeki talebinizi aldık.

Bilginize sunarız ki {BRAND_DOMAIN}, uluslararası düzeyde korunan ve tanınan bir markaya aittir. Ağ güvenliğinin sağlanması ile uluslararası kimlik sahteciliği (anti-spoofing) ve spam önleme düzenlemelerine uyum gereği, üçüncü bir kuruma ait bir alan adına açık yetkilendirme olmaksızın PTR kaydı tanımlayamıyoruz.

Bu güncellemeyi gerçekleştirebilmemiz için aşağıdakilerden birini iletebilir misiniz?

Resmî Yetkilendirme: {BRAND_DOMAIN} alan adının resmî yöneticisi tarafından, bu alt alan adının sunucunuzda kullanılmasına izin verildiğini gösteren belge veya yazılı onay.

Alternatif Alan Adı: Resmî yetkilendirmeniz bulunmuyorsa, PTR kaydı olarak tanımlanmak üzere sahibi olduğunuz veya yasal olarak kontrol ettiğiniz bir alan adı.

Gerekli doğrulama veya alternatif alan adı iletilene kadar talebinizi beklemede tutacağımızı belirtmek isteriz. Güvenli bir barındırma ortamının sürdürülmesindeki iş birliğiniz ve anlayışınız için teşekkür ederiz.

Saygılarımızla
```

---

## 2. Proof of control (technical variant) — EN

Use when the operator prefers verifiable evidence over paperwork.

```
Dear Customer,

Thank you for reaching out to us.

We have received your request to set the PTR (Reverse DNS) record for {IP} to {REQUESTED_PTR}.

The domain {BRAND_DOMAIN} is registered to a third party, so before we can create this record we need to verify that you control it. The simplest way to do this is a forward-confirmed lookup:

Please create an A record for {REQUESTED_PTR} pointing to {IP}, and let us know once it has propagated. We will verify the record and complete the PTR update immediately afterwards.

If you do not have access to the DNS zone of {BRAND_DOMAIN}, please provide an alternative hostname on a domain you control and we will configure that instead.

Best regards
```

## 2a. Proof of control — TR

```
Sayın Müşterimiz,

Bize ulaştığınız için teşekkür ederiz.

{IP} IP adresi için PTR (Ters DNS) kaydının {REQUESTED_PTR} olarak tanımlanması talebinizi aldık.

{BRAND_DOMAIN} alan adı üçüncü bir tarafa kayıtlı olduğundan, kaydı oluşturmadan önce bu alan adı üzerinde kontrol sahibi olduğunuzu doğrulamamız gerekiyor. Bunun en pratik yolu ileri yönlü doğrulamadır:

{REQUESTED_PTR} için {IP} adresini gösteren bir A kaydı oluşturup yayılmasının ardından bize bilgi verebilir misiniz? Kaydı doğruladıktan hemen sonra PTR güncellemesini tamamlayacağız.

{BRAND_DOMAIN} alan adının DNS bölgesine erişiminiz yoksa, kontrolünüzdeki bir alan adı üzerinden alternatif bir hostname iletmeniz yeterlidir.

Saygılarımızla
```

---

## 3. Restricted suffix (.gov / .gov.tr / .edu.tr / .mil.tr) — EN

```
Dear Customer,

Thank you for reaching out to us.

We have received your request to set the PTR record for {IP} to {REQUESTED_PTR}.

Domains under the {BRAND_DOMAIN} namespace are reserved for official institutions and their registration is restricted. We are therefore unable to configure a reverse DNS record pointing to this namespace without documentation from the institution that operates the domain.

Please provide either that documentation, or an alternative hostname on a domain you own, and we will proceed right away.

Best regards
```

---

## 4. Look-alike / typosquat domain — EN

Neutral wording: the point is verification, not an accusation.

```
Dear Customer,

Thank you for reaching out to us.

We have received your request to set the PTR record for {IP} to {REQUESTED_PTR}.

This hostname closely resembles a well-known third-party domain, so our abuse policy requires an additional verification step before we can configure it.

Could you please confirm the intended use of this hostname and create an A record for {REQUESTED_PTR} pointing to {IP}? Once we can verify the record, we will complete the update.

Best regards
```

---

## 5. Mail provider domain (gmail.com, outlook.com, …) — EN

```
Dear Customer,

Thank you for reaching out to us.

We have received your request to set the PTR record for {IP} to {REQUESTED_PTR}.

This hostname belongs to a public mail provider and is not operated by you or by us. A reverse DNS record pointing to it would fail forward-confirmed validation and would harm the mail reputation of the IP address rather than improve it.

For outbound mail we recommend a hostname on your own domain, for example mail.yourdomain.com, with a matching A record pointing to {IP}. Please let us know the hostname you would like to use.

Best regards
```
