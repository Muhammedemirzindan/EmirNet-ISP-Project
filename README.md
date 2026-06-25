# EmirNet-ISP-Project

Sıfırdan adım adım inşa edilen devasa, yedekli ve otomatize edilmiş kurumsal bir ISP (İnternet Servis Sağlayıcı) ağ mimarisi projesidir. Proje, gelişim sürecine göre sürüm sürüm (v0.x) büyütülerek dökümante edilecektir.

---

## 🚀 Sürüm: v0.1 - Çekirdek Kurulum (HQ Genesis)

Bu ilk sürümde, EmirNet Genel Merkez (HQ) ofisinin en temel ağ çekirdeği simüle edilmiştir. Amaç, altyapının merkez omurgasını ve dinamik adresleme mekanizmasını doğrulamaktır.

### 🛠️ Bu Sürümde Neler Yapıldı?
* **HQ Ağ Çekirdeği:** 1 Adet HQ_Router ve 1 Adet HQ_Switch entegrasyonu sağlandı.
* **Dinamik Adresleme (DHCP):** HQ_Router üzerinde yerel ağ için DHCP havuzu (`HQ_Pool`) konfigüre edildi. Uç cihazların (PC_1 ve PC_2) ağa dahil olurken geçtikleri **DDORA** (Discover, Offer, Request, Acknowledge) süreci başarıyla doğrulandı.
* **LAN Haberleşmesi:** `192.168.1.0/24` networkü içindeki uç cihazların yerel ağ seviyesinde ICMP (Ping) trafiği üzerinden sorunsuz ve gecikmesiz haberleştiği test edildi.

### 📊 v0.1 Topoloji ve Doğrulama
<img width="1653" height="733" alt="image" src="https://github.com/user-attachments/assets/938abfc4-9000-45a4-8c4c-bae61a9e94f8" />
