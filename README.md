# 🌟 FPGA Tabanlı RGB LED Solunum (Breathing) Efekti & PWM Kontrolü

![VHDL](https://img.shields.io/badge/Language-VHDL-blue.svg)
![FPGA](https://img.shields.io/badge/Target-Artix--7-orange.svg)
![Board](https://img.shields.io/badge/Board-Nexys4%20DDR-brightgreen.svg)

Bu proje, **Nexys4 DDR (Artix-7)** FPGA geliştirme kartı üzerinde VHDL kullanılarak tasarlanmış bir donanım projesidir. Proje temel olarak parametrik bir **PWM (Pulse Width Modulation)** sinyali üreterek kart üzerindeki iki adet RGB LED'in (LED16 ve LED17) parlaklığını dinamik olarak kontrol eder. 

LED'lerin parlaklığı yavaşça artıp azalarak estetik bir **"nefes alma" (breathing/fading)** efekti oluşturur. Ayrıca kart üzerindeki anahtarlar (switches) kullanılarak hangi renk kanallarının (Kırmızı, Yeşil, Mavi) aktif edileceği seçilebilir.

---

## ✨ Özellikler

- **Parametrik PWM Jeneratörü:** Frekansı ve saat hızı `generic` parametrelerle kolayca ayarlanabilen esnek PWM modülü.
- **Dinamik Duty Cycle (Görev Döngüsü):** 50Hz'lik bir zamanlayıcı kullanılarak görev döngüsü sürekli güncellenir.
- **Ters Orantılı Animasyon:** LED17'nin parlaklığı artarken, LED16'nın parlaklığı azalır (Çapraz solunum efekti).
- **Donanım Anahtarı (Switch) Kontrolü:** RGB renk kombinasyonları çalışma zamanında (run-time) kullanıcı tarafından anlık olarak değiştirilebilir.

---

## 📂 Dosya Yapısı

| Dosya Adı | Açıklama |
| :--- | :--- |
| `top.vhd` | **Ana Modül (Top Module):** PWM bileşenlerini birleştirir, 50Hz'lik zamanlayıcı ile animasyon mantığını işletir ve switch-LED bağlantılarını yapar. |
| `pwm.vhd` | **PWM Üretici:** 100MHz sistem saatini kullanarak istenilen frekansta (varsayılan 10 kHz) PWM sinyali üretir. |
| `tb_pwm.vhd` | **Testbench:** PWM modülünün görev döngüsü değişimlerine verdiği tepkileri Vivado simülasyon ortamında test etmek içindir. |
| `nexys4_ddr.xdc` | **Kısıtlama Dosyası (Constraints):** FPGA pin atamalarının (Saat, Anahtarlar ve RGB LED'ler) yapıldığı Xilinx fiziksel bağlantı dosyası. |

---

## 🛠️ Nasıl Çalışır?

Proje 100 MHz'lik sistem saatini (clk) kullanır. 
1. `pwm.vhd` modülü, kendisine verilen 7-bitlik `duty_cycle_i` girişine göre çıkış sinyalinin "High" (1) kalma süresini ayarlar.
2. `top.vhd` modülü içinde bir sayaç (counter) mekanizması vardır. Bu sayaç saniyede 50 defa (50Hz) tetiklenir.
3. Her tetiklenmede `LED17`'nin görev döngüsü artırılırken, `LED16`'nın görev döngüsü ondan çıkarılarak (`50 - duty_cycle`) ters bir dalga oluşturulur.
4. Çıkış sinyalleri, kart üzerindeki `SW0` - `SW5` anahtarları ile mantıksal "VE" (AND) işlemine tabi tutularak sadece istenen renklere iletilir.

### 🔌 Pin Bağlantıları (I/O Mapping)

| Switch (Giriş) | Renk Kanalı | İlgili LED |
| :---: | :--- | :---: |
| **SW0** (`J15`) | Mavi (Blue) | **LED 16** |
| **SW1** (`L16`) | Yeşil (Green) | **LED 16** |
| **SW2** (`M13`) | Kırmızı (Red) | **LED 16** |
| **SW3** (`R15`) | Mavi (Blue) | **LED 17** |
| **SW4** (`R17`) | Yeşil (Green) | **LED 17** |
| **SW5** (`T18`) | Kırmızı (Red) | **LED 17** |

---

## 🚀 Kurulum ve Kullanım

Bu projeyi kendi ortamınızda çalıştırmak için aşağıdaki adımları izleyin:

1. Bu depoyu bilgisayarınıza klonlayın:
   ```bash
   git clone https://github.com/KULLANICI_ADINIZ/proje-adi.git
   ```
2. **Xilinx Vivado** programını açın ve yeni bir proje oluşturun.
3. Hedef donanım olarak `xc7a100tcsg324-1` (Nexys 4 DDR) seçin.
4. `top.vhd` ve `pwm.vhd` dosyalarını *Design Sources* olarak ekleyin.
5. `nexys4_ddr.xdc` dosyasını *Constraints* olarak ekleyin.
6. Bitstream oluşturun (**Generate Bitstream**) ve kartınıza yükleyin (**Program Device**).
7. Kart üzerindeki ilk 6 anahtarı kullanarak LED animasyonlarının tadını çıkarın!

### 🧪 Simülasyon
Sadece PWM davranışını gözlemlemek isterseniz, `tb_pwm.vhd` dosyasını *Simulation Sources* olarak ekleyip Vivado üzerinden **Run Behavioral Simulation** seçeneğine tıklayarak farklı görev döngüsü senaryolarını dalga formu (waveform) penceresinde inceleyebilirsiniz.

---
*Bu proje IEEE standart VHDL kütüphaneleri kullanılarak geliştirilmiştir.*
