# EmirNet-ISP-Project

Sıfırdan adım adım inşa edilen devasa, yedekli ve otomatize edilmiş kurumsal bir ISP (İnternet Servis Sağlayıcı) ağ mimarisi projesidir. Proje, gelişim sürecine göre sürüm sürüm (v0.x) büyütülerek dökümante edilecektir.

---
## 🚀 Sürüm: v0.2 - WAN Entegrasyonu ve NAT Kurulumu (HQ Connect)

Bu sürümde, EmirNet Genel Merkez (HQ) ofisinin dış dünyaya (küresel internete) bağlantısı başarıyla simüle edilmiştir. Amaç, iç ağdaki özel IP adresine sahip cihazların tek bir çıkış noktasından internete erişmesini sağlamak ve NAT (Network Address Translation) mekanizmasını doğrulamaktır.

### 🛠️ Bu Sürümde Neler Yapıldı?

*   **WAN Bağlantısı (Internet):** `HQ_Router` üzerinde `Gi0/1` arayüzü yapılandırılarak internet bulutuna (`Net`) olan sınır bağlantısı tamamlandı.
*   **NAT / PAT Konfigürasyonu:** İç ağdaki `192.168.1.0/24` yerel IP bloğunun internete çıkabilmesi için router üzerinde dinamik NAT (PAT/Overload) kuruldu. `Gi0/0` arayüzü *inside*, `Gi0/1` arayüzü *outside* olarak tanımlandı.
*   **Küresel İnternet Erişimi:** Yerel ağdaki uç cihazların (`PC_1` ve `PC_2`) dış dünyadaki sunucularla ICMP (Ping) trafiği üzerinden sorunsuz, kayıpsız ve gecikmesiz şekilde haberleştiği test edilerek doğrulandı.

### 📊 v0.2 Topoloji ve Doğrulama
<img width="1011" height="680" alt="image" src="https://github.com/user-attachments/assets/b11050d3-2301-4d6f-989d-7e64ca8778f5" />
*Released: 27.06.2026*


## 🚀 Sürüm: v0.1 - Çekirdek Kurulum (HQ Genesis)

Bu ilk sürümde, EmirNet Genel Merkez (HQ) ofisinin en temel ağ çekirdeği simüle edilmiştir. Amaç, altyapının merkez omurgasını ve dinamik adresleme mekanizmasını doğrulamaktır.

### 🛠️ Bu Sürümde Neler Yapıldı?
* **HQ Ağ Çekirdeği:** 1 Adet HQ_Router ve 1 Adet HQ_Switch entegrasyonu sağlandı.
* **Dinamik Adresleme (DHCP):** HQ_Router üzerinde yerel ağ için DHCP havuzu (`HQ_Pool`) konfigüre edildi. Uç cihazların (PC_1 ve PC_2) ağa dahil olurken geçtikleri **DDORA** (Discover, Offer, Request, Acknowledge) süreci başarıyla doğrulandı.
* **LAN Haberleşmesi:** `192.168.1.0/24` networkü içindeki uç cihazların yerel ağ seviyesinde ICMP (Ping) trafiği üzerinden sorunsuz ve gecikmesiz haberleştiği test edildi.

### 📊 v0.1 Topoloji ve Doğrulama
<img width="1653" height="733" alt="image" src="https://github.com/user-attachments/assets/938abfc4-9000-45a4-8c4c-bae61a9e94f8" />
*Released: 25.06.2026*

