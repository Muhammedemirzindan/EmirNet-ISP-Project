# EmirNet-ISP-Project

Sıfırdan adım adım inşa edilen devasa, yedekli ve otomatize edilmiş kurumsal bir ISP (İnternet Servis Sağlayıcı) ağ mimarisi projesidir. Proje, gelişim sürecine göre sürüm sürüm (v0.x) büyütülerek dökümante edilecektir.

---
## 🚀 Sürüm: v0.5 - Dinamik Yönlendirme Omurgası, Kimlik Yönetimi ve Altyapı Optimizasyonu (The Dynamic Core)
Bu sürümde, EmirNet ISP omurgası ile müşteri arasındaki statik ve hantal yönlendirme yapısı tamamen terk edilerek, endüstri standardı olan dinamik yönlendirme mimarisine geçiş yapılmıştır. Ağın ölçeklenebilirliği, güvenliği ve kararlılığı paket seviyesinde doğrulanmış; altyapı insan müdahalesine gerek duymadan kendi rotasını çizebilecek seviyeye ulaştırılmıştır.

### 🛠️ Bu Sürümde Neler Yapıldı?

*   **Dinamik Omurga Entegrasyonu (OSPF Single-Area 0):**  EmirNet-PE-01 ile Customer1-CE-01 router'ları arasında OSPF (Open Shortest Path First) protokolü devreye alınmıştır. Statik rotalar tamamen silinerek, müşteri iç ağları ve ISP servis blokları dinamik olarak haritalandırılmış ve ağlar arası yakınsama (convergence) milisaniyeler seviyesine indirilmiştir.

*   **Deterministic Kimlik Yönetimi (Router-ID Hardening):** OSPF sürecinin ağdaki IP değişikliklerinden etkilenerek çökmesini (SPF recalculation) önlemek adına, router'ların kimlikleri otomatik seçimden çıkarılmıştır. EmirNet-PE-01 için 1.1.1.1, Customer1-CE-01 için 2.2.2.2 Router-ID'leri el ile (manually) atanarak altyapı kimlik mimarisi sabitlenmiştir.

*   **Hizmet Dağıtım Otomasyonu (Default Route Origination):** Müşteri tarafının internete çıkabilmesi için kurulan statik tanımlamalar modernize edilmiştir. PE router üzerinde üretilen default route, OSPF protokolü içerisinden default-information originate komutuyla müşteri router'ına dinamik bir enjeksiyonla aktarılmıştır.

*   **Ağ Güvenliği ve Trafik Optimizasyonu (Passive-Interface):** OSPF Hello paketlerinin, router bulunmayan ve sadece son kullanıcı/sunucu barındıran LAN bacaklarına fırlatılması engellenmiştir. İlgili arayüzler passive-interface olarak set edilerek hem gereksiz CPU/bant genişliği tüketimi sıfırlanmış hem de son kullanıcı bacağından gelebilecek sahte OSPF paket tehlikeleri (routing poisoning) bloke edilmiştir.

*   **Paket Seviyesinde Doğrulama (Wireshark Telemetry - Milestone):** Canlı hat üzerinden paket yakalama (packet capture) işlemi başlatılarak OSPF operasyonu izlenmiştir. Router'lar arası linkte 224.0.0.5 multicast adresine her 10 saniyede bir gönderilen Hello paketleri ve "Heartbeat" (canlı kalma) sinyalleri doğrulanmıştır. passive-interface yapılan bacaklarda ise tek bir paket akmadığı gözlemlenerek izolasyon başarıyla set edilmiştir.

### 📊 v0.5 Topoloji ve Doğrulama Hatları
<img width="1653" height="1030" alt="image" src="https://github.com/user-attachments/assets/658397f7-bdb3-4234-b1aa-8d0e7f0ade2e" />

### 🧠 Karşılaştığım Sorunlar (Problems Encountered) - v0.5

*   Sorun: Router-ID tanımlamaları yapılmasına ve konfigürasyonun kaydedilmesine rağmen, show ip ospf neighbor çıktısında komşuluk kimlikleri hâlâ eski arayüz IP'leri olarak görünmeye devam ediyordu.

*   Nedeni: Cisco IOS mimarisinde canlıda çalışan bir OSPF süreci (process) varken yapılan Router-ID değişikliklerinin mevcut komşulukları kesintiye uğratmamak adına anında devreye girmemesidir. Cihaz, eski kimlik tablosunu hafızada tutmaya devam ediyordu.

*   Çözüm: Operasyon modunda clear ip ospf process komutu tetiklenerek OSPF bellek tabloları temizlenmiş ve komşulukların yeni kimliklerle (1.1.1.1 ve 2.2.2.2) sıfır hata ile yeniden kurulması sağlanmıştır.

*Released: 01.07.2026*


---
## 🚀 Sürüm: v0.4 - Dağıtık Altyapıdan Merkezi Linux DHCP Yönetimine Geçiş ve Omurga Ölçekleme (The Centralized Core)

Bu sürümde EmirNet, router üzerinde çalışan yerel DHCP servisinden merkezi Linux tabanlı DHCP mimarisine geçirilmiştir. IP dağıtımı ağ cihazından ayrılarak bağımsız bir sunucuya taşınmış, farklı VLAN'lardaki istemcilerin DHCP Relay (ip helper-address) mekanizması ile tek bir merkezi DHCP sunucusundan dinamik IP adresi alması sağlanmıştır. Böylece ağ yönetimi daha ölçeklenebilir ve gerçek ISP mimarisine daha uygun hale getirilmiştir.

### 🛠️ Bu Sürümde Neler Yapıldı?

*   **Merkezi Linux DHCP Sunucusu:** Ubuntu Server üzerinde isc-dhcp-server kurulmuş ve EmirNet ağı için merkezi DHCP hizmeti devreye alınmıştır.

*   **Router DHCP'den Merkezi Sunucuya Geçiş:** HQ tarafında router üzerinde çalışan DHCP servisi kaldırılarak IP dağıtım görevi tamamen Linux sunucusuna taşınmıştır.

*   **Yeni Sunucu VLAN'ı:** DHCP sunucusunu istemci ağlarından mantıksal olarak ayırmak amacıyla yeni bir VLAN 30 oluşturulmuştur.

-PE Router üzerinde Gi0/0.30 sub-interface oluşturuldu.

-Aggregation Switch üzerinde VLAN 30 ile eşleştirildi.

-Linux DHCP Server bu VLAN içerisine konumlandırıldı.

*   **Çoklu DHCP Havuzları (Multi-Subnet Pools):** Merkezi DHCP sunucusu üzerinde üç farklı ağ için bağımsız IP havuzları oluşturuldu. Her VLAN kendi gateway, DNS ve IP dağıtım aralığına sahip olacak şekilde yapılandırıldı.

-VLAN 10 → 192.168.1.0/24

-VLAN 20 → 192.168.20.0/24

-VLAN 30 → 192.168.30.0/24

*   **DHCP Relay (IP Helper Address):** Router üzerinde ip helper-address yapılandırılarak farklı VLAN'lardaki DHCP Discover yayınlarının merkezi DHCP sunucusuna yönlendirilmesi sağlandı.

*   **Merkezi IP Yönetimi:**

-IP adresi

-Subnet Mask

-Default Gateway

-DNS Server

İstemcilerin bu bilgileri DHCP üzerinden otomatik dağıtılacak şekilde yapılandırıldı.

### 📊 v0.4 Topoloji ve Doğrulama Hatları
<img width="1912" height="755" alt="image" src="https://github.com/user-attachments/assets/ead624c7-567c-4e6f-978b-883a9c41ec77" />

### 🧠 Karşılaşılan Sorunlar (Problems Encountered) — v0.4

*   **Sorun:** Projeye merkezi bir Linux DHCP Server eklemek amacıyla Ubuntu Server, PNETLab topolojisine dahil edildi ve statik IP adresi (192.168.30.5) yapılandırıldı. Ancak isc-dhcp-server paketini kurmak için gerekli olan apt update ve apt install işlemleri normal çalışmıyordu. Paket indirme hızı yaklaşık 750 B/s seviyesinde kalıyor, daha sonra ise işlemler tamamen "Waiting for headers" aşamasında takılıyordu. Ubuntu paket kaynaklarının Türkiye (TR) mirror'larına yönlendirilmesi de sorunu çözmedi.
Sorunun paket yöneticisinden kaynaklandığı düşünülerek Ubuntu 22.04'e geçildi ve ek paket gerektirmeyen systemd-networkd kullanılarak DHCP sunucusu kurulmaya çalışıldı. Yapılan yapılandırmalar sonucunda DHCP Discover paketlerine cevap üretilebilse de DHCP Offer paketleri istemciye ulaşmadı ve istemciler dinamik IP adresi alamadı.

*   **Nedeni:** Yapılan testler sonucunda problemin DHCP protokolünden değil, PNETLab içerisindeki Linux node'unun internet erişim yapısından kaynaklandığı anlaşıldı. Aynı Ubuntu Server imajı VMware üzerinde bağımsız bir sanal makine olarak çalıştırıldığında sistem doğrudan modemden IP adresi aldı ve tüm paketler normal hızda indirilebildi. Bu durum, işletim sistemi veya paket yöneticisinden ziyade PNETLab içerisindeki Linux node'unun internet bağlantısının sorunlu olduğunu gösterdi.

*   **Çözüm:** VMware üzerinde yapılan testten elde edilen sonuç doğrultusunda PNETLab topolojisine yeni bir Linux node eklendi ve ayrı bir ManagementCloud0 bağlantısı oluşturularak sunucunun doğrudan modem üzerinden internete erişmesi sağlandı. Bu yapılandırmanın ardından apt update ve isc-dhcp-server kurulumu sorunsuz şekilde tamamlandı. Sonrasında DHCP havuzları yapılandırıldı, sunucuya statik IP adresi atandı, gerekli servis ayarları tamamlandı ve router üzerinde DHCP Relay (ip helper-address) yapılandırılarak istemcilerin farklı VLAN'lardan dinamik IP adresi alması başarıyla sağlandı.

*   **Kazanım:** Bu süreç yaklaşık 8 saatlik troubleshooting çalışması gerektirdi. Sorun ilk bakışta DHCP veya Ubuntu kaynaklı gibi görünmesine rağmen, yapılan adım adım testler sayesinde problemin sanallaştırma ortamı ve ağ mimarisinden kaynaklandığı tespit edildi. Bu çalışma sayesinde yalnızca merkezi bir DHCP servisi devreye alınmadı; aynı zamanda problem çözme, izolasyon testi (VMware ve PNETLab karşılaştırması), Linux servis yönetimi ve ağ altyapısı analizi konularında önemli deneyim kazanıldı.

### 🎯 Kazanımlar

Bu sürümle birlikte EmirNet yalnızca paket ileten bir ağ olmaktan çıkıp, istemcilere ağ hizmeti sağlayan gerçek bir servis altyapısına dönüşmeye başlamıştır. Merkezi DHCP mimarisi sayesinde IP yönetimi kolaylaştırılmış, ağın ölçeklenebilirliği artırılmış ve ileride eklenecek DNS, NTP, AAA ve diğer merkezi servisler için gerekli temel altyapı hazırlanmıştır.

*Released: 30.06.2026*


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

### 🧠 Karşılaştığım Sorunlar (Problems Encountered) - v0.3

*   Sorun: Kurumsal müşteri tarafındaki bilgisayar (Customer1-PC-1) kendi ağındaki gateway'e ve ISP bacağına ping atabilirken, dış dünyaya (8.8.8.8) erişmeye çalıştığında paketler timeout yiyordu.

*   Nedeni: EmirNet-PE-01 (ISP Router) ile Customer1-CE-01 (Müşteri Router) arasında fiziksel hat aktif edilmiş ve IP'ler verilmiş olmasına rağmen, ISP router'ının müşterinin iç yerel ağına (192.168.20.0/24) giden yolu bilmemesiydi. Paket internete çıksa bile, dönüş trafiğinde ISP router'ı hedef adresi tanımadığı için paketleri düşürüyordu.

*   Çözüm: EmirNet-PE-01 router'ı üzerinde, müşteri iç ağına doğru statik bir yönlendirme rotası tanımlandı.

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

### 🧠 Karşılaştığım Sorunlar (Problems Encountered) - v0.2

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

