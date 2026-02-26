# MOSFET SOA Analyzer

> **Tek dosya, sıfır bağımlılık, tarayıcıda çalışan** MOSFET Safe Operating Area analiz aracı.  
> Datasheet SOA grafiği üzerine kalibre edilmiş eğriler çizer, termal derating ve pulse-width interpolasyonu hesaplar.

---

## 🚀 Kurulum

Hiçbir şey yüklemenize gerek yok.

```bash
# Repo'yu klonla
git clone https://github.com/kullaniciadi/mosfet-soa-analyzer.git

# Dosyayı tarayıcıda aç
open mosfet_soa_analyzer.html
```

Veya doğrudan [GitHub Pages](https://kullaniciadi.github.io/mosfet-soa-analyzer/) üzerinden kullanın.

---

## 📋 Özellikler

### 1. Grafik Yükleme
- PNG / JPG formatında SOA grafiği yükleyin (sürükle-bırak desteklenir)
- Görsel opaklığı ayarlanabilir (%10–100)

### 2. Eksen Kalibrasyonu (4 Nokta)
Grafiğin 4 köşesini tıklayıp gerçek Vds/Id değerlerini girerek piksel ↔ koordinat dönüşümü kurulur.

```
Sol-Alt  → Vds_min, Id_min
Sol-Üst  → Vds_min, Id_max
Sağ-Alt  → Vds_max, Id_min
Sağ-Üst  → Vds_max, Id_max
```

Kalibrasyon tamamlandıktan sonra fare, grafik üzerinde gerçek Vds/IDS/P değerlerini anlık gösterir.

### 3. Eğri İşaretleme (Manuel Trace)
Orijinal eğri üzerindeki noktalar tıklanarak kaydedilir. Uygulama log-log interpolasyon yaparak eğriyi çizer ve derated versiyonunu üretir.

### 4. Nokta İşaretleme (Pin Markers)
Grafik üzerine koordinat noktaları eklenebilir. Her noktada otomatik olarak:
- **Vds** değeri
- **IDS** değeri  
- **P = Vds × IDS** güç değeri

gösterilir. 4 farklı sembol (● ✕ ◆ ▼), özel etiket ve eksen çizgisi desteği mevcuttur.

### 5. Serbest Metin (Annotations)
Grafiğin istediğiniz yerine metin ekleyin. Boyut, renk, açı ve arka plan kutu özelleştirilebilir.

### 6. Pulse Width İnterpolasyon

Datasheet'teki iki bilinen eğri noktasından power-law modeli otomatik hesaplanır:

```
IDS(tPW) = a × (tPW)^m

m = ln(IDS1 / IDS2) / ln(tPW1 / tPW2)
a = IDS1 / (tPW1)^m
```

**Örnek** (CSD17570Q5B @ VDS = 12 V):
```
IDS(10 ms)  = 7.3 A
IDS(100 ms) = 4.1 A
→ m = -0.25,  a = 12.9
→ IDS(20 ms) = 12.9 × (20)^-0.25 = 6.1 A
```

İstediğiniz süreleri virgülle girerek tüm değerleri tek seferde hesaplayabilirsiniz.

### 7. Termal Derating

Datasheet lineer derating formülü:

```
IDS(Tc) = IDS(25°C) × (TJ_max − Tc) / (TJ_max − 25°C)
```

**Örnek** (TJ_max = 150°C, Tc = 100°C):
```
DF = (150 − 100) / (150 − 25) = 50 / 125 = 0.4
IDS(100°C) = 7.3 A × 0.4 = 2.92 A
```

### 8. Log-Log Grid
Eksen kalibrasyonu tamamlandığında, grafik üzerine major/minor log-log çizgiler ve sayısal etiketler çizilir.

### 9. Stil Ayarları
| Parametre | Açıklama |
|---|---|
| Ref / Derated eğri kalınlığı | px cinsinden |
| İnterpolasyon çizgisi kalınlığı | px cinsinden |
| Grid major / minor kalınlık | px cinsinden |
| Pin & crosshair çizgi kalınlığı | px cinsinden |
| Eğri glow (ışıma) | 0–30 |
| Grid / Pin / İnterp / Crosshair yazı boyutu | px cinsinden |
| Grid renk & opaklık | Renk seçici + % |

### 10. Cursor Crosshair
Fare grafik üzerindeyken:
- Dikey + yatay çizgi takip eder
- X ekseninde Vds, Y ekseninde IDS değeri gösterilir
- Üst barında **VDS · IDS · P** anlık güncellenir

---

## 🔧 Kullanım Akışı

```
1. SOA grafiğini yükle (PNG/JPG)
         ↓
2. Kalibrasyonu başlat → 4 köşeyi tıkla → değerleri gir
         ↓
3. Eğrileri işaretle (orijinal eğri üzerinden nokta tıkla)
         ↓
4. Termal parametreleri gir: TJ_max, Tc
         ↓
5. [Opsiyonel] Pulse-width interpolasyonu hesapla
         ↓
6. [Opsiyonel] Nokta/metin ekle, stilleri ayarla
         ↓
7. PNG olarak indir
```

---

## 📐 Formüller

### Termal Derating
$$I_{DS}(T_C) = I_{DS}(25°C) \times \frac{T_{J(max)} - T_C}{T_{J(max)} - 25°C}$$

### Pulse-Width Power Law
$$I_{DS}(t_{PW}) = a \times (t_{PW})^m$$

$$m = \frac{\ln\left(\frac{I_{DS1}}{I_{DS2}}\right)}{\ln\left(\frac{t_{PW1}}{t_{PW2}}\right)}, \quad a = \frac{I_{DS1}}{(t_{PW1})^m}$$

### Koordinat Dönüşümü (Log-Log Kalibrasyon)
Piksel → gerçek değer dönüşümü en küçük kareler (least squares) yöntemiyle hesaplanır:

$$\log_{10}(V_{DS}) = s_x \cdot p_x + b_x$$
$$\log_{10}(I_{DS}) = s_y \cdot p_y + b_y$$

---

## 🖥️ Teknik Detaylar

| Özellik | Değer |
|---|---|
| Teknoloji | Vanilla HTML + CSS + Canvas 2D API |
| Bağımlılık | Yok (Google Fonts CDN opsiyonel) |
| Dosya sayısı | 1 |
| Tarayıcı desteği | Chrome, Firefox, Edge, Safari (modern) |
| Versiyon | v8.0 |

**Canvas katmanları:**
- `#MC` — Ana katman: görsel, grid, eğriler, nokta etiketleri
- `#OC` — Overlay: kalibrasyon noktaları, aktif trace noktaları
- `#CC` — Cursor: crosshair (PNG export'a dahil edilmez)

---

## 📤 PNG Export

"PNG İndir" butonu, grafik + tüm çizimler + nokta etiketleri + serbest metinler dahil olmak üzere tüm katmanları birleştirir. Cursor crosshair export'a dahil edilmez.

---

## 💡 İpuçları

- **Kalibrasyon hassasiyeti:** Grafik köşelerini mümkün olduğunca doğru tıklayın. Zoom yaparak tarayıcıda büyütebilirsiniz (`Ctrl +`).
- **Eğri işaretleme:** Eğrinin kritik bükülme noktalarına öncelik verin, 5–10 nokta genellikle yeterlidir.
- **Çoklu eğri:** Her pulse süresi için ayrı renk seçerek birden fazla eğri işaretleyebilirsiniz.
- **Grid sayıları:** Log-Log grid açıkken eksen değerleri otomatik hesaplanır — kalibrasyon olmadan çalışmaz.

---

## 📄 Lisans

MIT License — serbestçe kullanabilir, değiştirebilir ve dağıtabilirsiniz.
