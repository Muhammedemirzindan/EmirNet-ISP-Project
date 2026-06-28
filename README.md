# EmirNet-ISP-Project

Sıfırdan adım adım inşa edilen devasa, yedekli ve otomatize edilmiş kurumsal bir ISP (İnternet Servis Sağlayıcı) ağ mimarisi projesidir. Proje, gelişim sürecine göre sürüm sürüm (v0.x) büyütülerek dökümante edilecektir.

---
## 🚀 Sürüm: v0.3 - Metro Ethernet Toplama Altyapısı ve Kurumsal Müşteri Aktivasyonu (The Corporate Dawn)

Bu sürümde, EmirNet yerel bir ağ laboratuvarı modelinden çıkarak gerçek bir İnternet Servis Sağlayıcı (ISP) omurgasına dönüştürülmüştür. Şirket içi yönetim ağı ile kurumsal müşteri trafiği hem fiziksel hem de mantıksal (VLAN) olarak tamamen izole edilmiş; sanal alt arayüz (Sub-interface) mimarisiyle tek bir fiziksel hat üzerinden güvenli ve bağımsız internet akışı başarıyla doğrulanmıştır.

### 🛠️ Bu Sürümde Neler Yapıldı?

*   **Profesyonel Kurumsal Kimlik (Naming Standartları):** Sektör standardı mimarilere geçiş yapılarak cihazlar EmirNet-PE-01 (Provider Edge), EmirNet-AGG-01 (Aggregation Switch) ve Customer1-CE-01 (Customer Edge) olarak yeniden isimlendirildi.

*   **Fiziksel ve Mantıksal İzolasyon (HQ LAN Ayrımı):** EmirNet genel merkez bilgisayarları (HQ), güvenlik ve operasyonel rol ayrımı gereği toplama switch'inden alınarak tamamen bağımsız bir katmana, EmirNet-HQ-SW-01 switch'i altına taşındı.

*   **Genişletilebilir IP ve VLAN Matrisi:** İleride yapılacak rota özetlemeye (Route Aggregation) uygun, 10.10.X.X/30 tabanlı bir WAN otobanı kurgulandı. İlk kurumsal müşteri için VLAN 20 ve 10.10.10.0/30 bloğu tanımlandı.

*   **Sub-Interface (Sanal Bölümleme) Entegrasyonu:** EmirNet-PE-01 router'ının ana fiziksel portu (Gi0/0) yazılımsal olarak dilimlere ayrıldı (Gi0/0.10 ve Gi0/0.20). Bu sayede tek bir kablo içinden hem şirket içi trafik hem de müşteri trafiği birbirine sızmadan taşındı.

*   **İlk Kurumsal İnternet Erişimi (Milestone):** Customer1-PC-1 cihazından küresel internete (8.8.8.8) atılan pinglerin başarıyla dönmesiyle hat aktif edildi. Müşterinin iç ağımızdaki kritik IP bloklarına erişimi engellenerek tam izolasyon doğrulandı.

### 📊 v0.3 Topoloji ve Doğrulama Hatları
<img width="1810" height="799" alt="image" src="https://github.com/user-attachments/assets/6cd5bc57-73b9-445c-82f7-c838d2beabf7" />

## 🧠 Karşılaştığım Sorunlar (Problems Encountered) —

*   Sorun: Kurumsal müşteri tarafındaki bilgisayar (Customer1-PC-1) kendi ağındaki gateway'e ve ISP bacağına ping atabilirken, dış dünyaya (8.8.8.8) erişmeye çalıştığında paketler timeout yiyordu.

*   Nedeni: EmirNet-PE-01 (ISP Router) ile Customer1-CE-01 (Müşteri Router) arasında fiziksel hat aktif edilmiş ve IP'ler verilmiş olmasına rağmen, ISP router'ının müşterinin iç yerel ağına (192.168.20.0/24) giden yolu bilmemesiydi. Paket internete çıksa bile, dönüş trafiğinde ISP router'ı hedef adresi tanımadığı için paketleri düşürüyordu.

*   Çözüm: EmirNet-PE-01 router'ı üzerinde, müşteri iç ağına doğru statik bir yönlendirme rotası tanımlandı.


### 💡 Geliştirici Notu
Bugün bir şey fark ettim: Ben bu iş üzerine çalışmayı gerçekten çok seviyorum. Sıfırdan bir şeyler üretmek, laboratuvarı adım adım geliştirmek ve sistemin büyüdüğünü görmek mutluluk veriyor. İleride ne kadar başarılı olurum ya da bu yol beni nereye götürür bilmiyorum ama bildiğim tek bir şey var, ben bu işi yaparken kendimi buluyorum ve bu yolda devam edeceğim.


*Released: 28.06.2026*


---
## 🚀 Sürüm: v0.2 - WAN Entegrasyonu ve NAT Kurulumu (HQ Connect)

Bu sürümde, EmirNet Genel Merkez (HQ) ofisinin dış dünyaya (küresel internete) bağlantısı başarıyla simüle edilmiştir. Amaç, iç ağdaki özel IP adresine sahip cihazların tek bir çıkış noktasından internete erişmesini sağlamak ve NAT (Network Address Translation) mekanizmasını doğrulamaktır.

### 🛠️ Bu Sürümde Neler Yapıldı?

*   **WAN Bağlantısı (Internet):** `HQ_Router` üzerinde `Gi0/1` arayüzü yapılandırılarak internet bulutuna (`Net`) olan sınır bağlantısı tamamlandı.
*   **NAT / PAT Konfigürasyonu:** İç ağdaki `192.168.1.0/24` yerel IP bloğunun internete çıkabilmesi için router üzerinde dinamik NAT (PAT/Overload) kuruldu. `Gi0/0` arayüzü *inside*, `Gi0/1` arayüzü *outside* olarak tanımlandı.
*   **Küresel İnternet Erişimi:** Yerel ağdaki uç cihazların (`PC_1` ve `PC_2`) dış dünyadaki sunucularla ICMP (Ping) trafiği üzerinden sorunsuz, kayıpsız ve gecikmesiz şekilde haberleştiği test edilerek doğrulandı.

### 📊 v0.2 Topoloji ve Doğrulama
<img width="1011" height="680" alt="image" src="https://github.com/user-attachments/assets/b11050d3-2301-4d6f-989d-7e64ca8778f5" />

## 🧠 Karşılaştığım Sorunlar (Problems Encountered)

*   Sorun: HQ_Router tüm konfigürasyonlar doğru olmasına rağmen dış dünyaya (Net bulutuna) ping atamıyor, paketler timeout yiyordu. Wireshark ile incelendiğinde, router'ın gönderdiği ARP ve DHCP isteklerine karşı taraftan (sanal modem/gateway) hiçbir cevap gelmediği görüldü.

*   Nedeni: Sorun Cisco komutlarında değil, simülatörün (PNet Lab) arka planındaydı. Üst üste yapılan denemeler yüzünden router ile bilgisayarın sanal ağ kartı (modem bağlantısı) arasındaki o görünmez köprü kilitlenmişti. Paketler fiziksel ağa ulaşamıyordu.

*   Çözüm: Router ile bulut arasındaki kablo tamamen silindi ve yeniden bağlandı. Bu basit sanal "sök-tak" işlemi kilitlenen köprüyü sıfırladı; router modemi algılayıp tıkır tıkır IP'sini aldı ve internet kapısı açıldı.

*Released: 27.06.2026*


---
## 🚀 Sürüm: v0.1 - Çekirdek Kurulum (HQ Genesis)

Bu ilk sürümde, EmirNet Genel Merkez (HQ) ofisinin en temel ağ çekirdeği simüle edilmiştir. Amaç, altyapının merkez omurgasını ve dinamik adresleme mekanizmasını doğrulamaktır.

### 🛠️ Bu Sürümde Neler Yapıldı?
* **HQ Ağ Çekirdeği:** 1 Adet HQ_Router ve 1 Adet HQ_Switch entegrasyonu sağlandı.
* **Dinamik Adresleme (DHCP):** HQ_Router üzerinde yerel ağ için DHCP havuzu (`HQ_Pool`) konfigüre edildi. Uç cihazların (PC_1 ve PC_2) ağa dahil olurken geçtikleri **DDORA** (Discover, Offer, Request, Acknowledge) süreci başarıyla doğrulandı.
* **LAN Haberleşmesi:** `192.168.1.0/24` networkü içindeki uç cihazların yerel ağ seviyesinde ICMP (Ping) trafiği üzerinden sorunsuz ve gecikmesiz haberleştiği test edildi.

### 📊 v0.1 Topoloji ve Doğrulama
<img width="1653" height="733" alt="image" src="https://github.com/user-attachments/assets/938abfc4-9000-45a4-8c4c-bae61a9e94f8" />
*Released: 25.06.2026*

