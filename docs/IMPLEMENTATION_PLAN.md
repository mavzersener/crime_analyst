# Implementation Plan — Poisson MVP on RTX 5090

Güncelleme: 2026-10-09. Temel audit: main commit `714369880fc3a4f99445116e874091067343bf27`.
Önce [Architecture Gap Analysis](ARCHITECTURE_GAP_ANALYSIS.md) okunmalı. Bu plan çalışma kodu veya başarı raporu değildir. Mevcut görev yalnız belge üretimidir; sonraki uygulama ayrı yetki gerektirir.

## Hedef ve faz sırası

İlk teslim: Windows 11 Pro / RTX 5090 32 GB üzerinde aynı Poisson(λ=3) zaman çizelgesinden tr, en, zh-CN için üç 45–60 saniyelik 1080×1920 30 fps H.264/AAC MP4, UTF-8 SRT, metadata ve QC raporu. Veriler varsayımsal/sentetik. Kayıtlı anlatım öncelikli; yoksa açıkça etiketli yer tutucu teknik pilot. Klonlama, NB2, batch ve UI bu hedefin sonrasındadır.

Fazlar additive ilerler; web prototipi veya geçmiş yeniden yazılmaz. Bir fazın kabul kanıtı olmadan sonraki bağımlı faz tamamlandı sayılmaz. GPU hatasında CPU çıktısı oluşturulabilir ancak RTX 5090 kabulü açık kalır.

## Faz 0A — Repository audit (bu görev)

Teslim: ARCHITECTURE_GAP_ANALYSIS.md, ardından bu plan.

Kabul:
- Altı mevcut dosya ve recursive tree sabit commit'ten incelendi.
- Yeniden kullanılabilir metin/tema; web bilimsel hesap boşlukları; lisans ve kurulum eksikleri kaydedildi.
- Gerçek Linux oturumu ile hedef Windows/GPU ayrıldı; çalışan renderer veya kurulmuş ses modeli iddiası yok.
- Birincil kaynak lisans/dil incelemesi yapıldı; çözülmemiş model/artifact lisansları açık işaretlendi.

Durum: belge incelemesi tamamlandı; hedef makine doğrulaması aşağıdaki ayrı fazdır.

## Faz 0B — Hedef makine, lisans ve kurulum ön kontrolü

Uygulama kodundan önce hedef Windows'ta araç incelemesi yap. OS/Python/sürücü/GPU, FFmpeg build ve gerçek NVENC denemesi, fontlar ve exact paket wheel uyumluluğu kaydedilsin. Kurulum gerekirse ayrı sonraki yetkilendirilmiş çalışma içinde yapılır.

Teslim: ortam raporu; seçilmiş artifact/sürüm/hash listesi; notices/lisans tablosu; bloke eden konular.

Kabul:
- PowerShell Get-ComputerInfo, py -0p, nvidia-smi ve FFmpeg/ffprobe version/buildconf çıktıları rapora alınır.
- h264_nvenc 2 saniyelik 1080×1920 testini gerçekten tamamlar; ffprobe H.264 ve doğru boyutu doğrular. Encoder listesi tek başına başarı değildir.
- Python 3.12 x64 ile NumPy/SciPy/Matplotlib Windows wheel planı doğrulanır; paket sürümleri Linux ortamından kopyalanmaz.
- Font kaynak/lisans/hash ve Türkçe/Chinese/formül glyph denemesi onaylanır.
- Proje LICENSE seçimi sahibiyle netleşir; third-party ve FFmpeg binary dağıtım yükümlülükleri açık kalmaz. Private/local usage ile redistributable release ayrılır.
- TTS çekirdek kuruluma dahil değildir; CUDA toolkit/PyTorch video için gereksizdir.
- Hedef erişimi yoksa bu faz blocked olarak kalır; GPU çalışıyor denmez.

## Faz 1A — Minimal paket, CLI ve episode sözleşmesi

Uygulama yetkisi ve Faz 0B kanıtı sonrasında: pyproject, minimum runtime/test bağımlılıkları ve Windows için sürüm kilidi; episode JSON doğrulama; doctor/validate/dry-run CLI. JSON için standard-library yaklaşımı yeterli; gereksiz servis/framework eklenmez.

Teslim: çalıştırılabilir minimal paket, environment manifest, episode şeması ve örnek Poisson girdisi.

Önerilen komutlar (henüz mevcut değil):
- `python -m crime_analyst doctor --encoder h264_nvenc`
- `python -m crime_analyst validate --episode content/episodes/poisson_intro/episode.json`

Kabul: temiz venv kurulumunda pip check başarılı; required fields/locale/λ/time validation; yanlış girdi sıfır olmayan exit; dry-run render veya ağ çağrısı yapmaz; Windows boşluk ve Unicode yolları çalışır. Yeni kişisel kayıt/cache yolları gitignore ile korunur; mevcut ignore kapsamı bozulmaz.

## Faz 1B — Bağımsız Poisson doğrulaması ve sentetik veri

Teslim: stats katmanı, seed/RNG/parametre manifestli sentetik örnek; kesikli teorik PMF.

Kabul:
- λ=3 için P(0)=0.04978706836786394; SciPy ve math.exp/factorial bağımsız referans farkı <=1e-12.
- Negatif/sonlu olmayan λ ve geçersiz support reddedilir; PMF nonnegative; gösterilen aralık + tail toplamı toleransla 1.
- Sabit seed/RNG ve kilitli ortam tekrar aynı veri üretir.
- Teorik PMF ile örnek histogramı ayrılır; bütün sahnelerde yerelleştirilmiş varsayımsal etiket bulunur.
- `python -m pytest tests/test_stats.py tests/test_synthetic.py` (gelecekte eklenecek) geçer; sonuçlar gerçekten çalıştırılınca kaydedilir.

## Faz 1C — Üç dil içerik, ses ve ortak zaman çizelgesi

Mevcut metinler başlangıçtır. Her dil için title, sahne metni ve altyazı cue'ları hazırlanır. Varsayımsal örnek metin içinde de açıklaşır. Öncelikli ses yöntemi üç dil doğrudan kayıttır; yoksa placeholder modu seçilir.

Teslim: episode/locale girdileri, terim listesi, ortak scene ID/start/end, yerel ses dosyalarına adapter ve rıza durumu.

Kabul:
- TR/EN bilimsel dil incelemesi; Mandarin yetkin insan okuma/dinleme incelemesi kaydedilir.
- Poisson varsayımları ve %4,98 hesap doğru; neden-sonuç veya gerçek suç tahmini iddiası yok.
- Üç dil aynı sahne sınırlarında kalır. En uzun onaylı segment + tamponla toplam 45–60 sn; taşan ses kesilmez.
- SRT cue'ları monotonic, sahneye bağlı ve UTF-8; gerçek sesle hedef <=100 ms hizalama ve insan incelemesi.
- Kayıtlar user-owned/izinli ve yerel ignored klasörde; repo veya servise yüklenmez.
- Placeholder final frame ve metadata'da görünür biçimde belirtilir; dil/telaffuz onayı yerine geçmez.

## Faz 1D — Ortak görseller ve kısa üç dil render

Teslim: Matplotlib Agg kesikli PMF sahneleri, tek master timeline, yerel başlık/altyazı overlay; üç dil için 5–10 saniyelik smoke render.

Kabul:
- SciPy katmanındaki veri kullanılır; web örnek eğrileri kopyalanmaz.
- Bütün gerçek metinlerin glyph'leri ve 1080×1920 safe margins insan tarafından izlenir; missing glyph, taşma veya kesik metin yok.
- Aynı scene/frame sınırları üç dilde korunur; font ve encoder seçimi manifestte görünür.
- FFmpeg argüman listeleri Windows path'lerini güvenli geçirir; başarısız render açık hata verir.
- h264_nvenc hedefte kısa üç dil testini geçer; CPU fallback ancak açık kullanıcı seçimiyle ve raporlanarak.

## Faz 1E — Tam Poisson MVP ve QC

Önerilen komutlar:
`python -m crime_analyst render --episode content/episodes/poisson_intro/episode.json --languages tr en zh-CN --audio-mode recorded --encoder h264_nvenc`
`python -m crime_analyst verify --run outputs/<run-id>`

Teslim: tr/en/zh-CN MP4 + SRT + metadata; run-manifest.json, qc-report.json; Windows temiz kurulum ve tekrar çalıştırma yönergesi.

Kabul:
- Her MP4 ffprobe ile video H.264, 1080×1920, 30 fps, yuv420p ve audio AAC stream doğrulamasını geçer; ses sample rate ör. 48 kHz açık kaydedilir.
- Her çıktı 45–60 sn; üç dilin süre farkı ve A/V bitiş farkı <= bir video kare + bir AAC paket süresi.
- SRT ve sahne sınırları geçerli; konuşma/altyazı hedef <=100 ms, insan izleme/dinleme onayı.
- Kayıtlı anlatım veya açık placeholder, lokal başlık, okunabilir altyazı ve varsayımsal etiketi üç dilde mevcut.
- Negatif integration testleri: eksik font/locale/audio, ses taşması, encoder hatası, bozuk episode açık başarısızlık üretir.
- Taze venv + kilitli bağımlılıklar ile komut yeniden çalışır; source/input/font hash, seed ve encoder kayıtlıdır.
- Hedef RTX 5090 GPU render kanıtı vardır; henüz erişilemiyorsa MVP yalnız CPU teknik prototip olarak raporlanır.
- Çıktı ve özel sesler git'e girmez; Instagram veya başka dış yayın yok.

Yer tutucuyla bu fazın teknik medya pipeline'ı kabul edilebilir; doğal üç dil anlatımlı tamamlanmış ders kabulü açık kalır. Bit-for-bit MP4 aynılığı vaat edilmez.

## Faz 2A — Ses modeli lisans ve dil elemesi

Poisson teknik MVP sonrasında, ses klonlaması ayrı yetki kapsamında değerlendirilir.

Teslim: exact model/checkpoint revision, kod/ağırlık lisans metni ve hash, resmi TR/EN/ZH support kanıtı, ticari kullanım kararı.

Kabul: aday kod lisansı ağırlık lisansı yerine kullanılmaz; XTTS CPML ticari izin doğrulanmadan aktif olmaz; F5 pretrained CC-BY-NC ticari akıştan çıkarılır; Melo/Qwen Türkçe eksikliği açık tutulur. Hiçbiri kapıyı geçmezse recorded/placeholder çalışmaya devam eder.

## Faz 2B — Rızalı yerel GPU benchmark ve dinleme

Teslim: izole TTS ortamı, adapter, üç dil telaffuz/kimlik örnekleri; süre, VRAM, RTF ve sürüm raporu.

Kabul: açık rıza + user-owned referans; hedef Windows/RTX 5090 gerçek inference; seçilen PyTorch wheel/Blackwell uyumu; peak VRAM < mevcut bütçe; Poisson/λ/sayı telaffuzu ve kimlik için insan onayı. Başarılı model bile Faz 1E süre/hizalama/QC sınamalarından geçmeden prod audio olmaz. Kullanıcı kaydı/modeli dışarı yüklenmez.

## Faz 3 — Poisson vs Negative Binomial

Teslim: ayrı additive episode; matched mean μ=3, NB2 α>0, Var=μ+αμ²; SciPy n=1/α, p=n/(n+μ).

Kabul: mean/variance bağımsız referans; α→0 Poisson limiti; iki modelin doğru teorik destekleri ve açıklamaları; sentetik seed; üç dil incelemesi ve Faz 1E bütün medya/QC kontrolleri.

## Faz 4 — Batch ve gerekirse yerel UI

Teslim: tekrarlanabilir episode batch, cache/input hashes, aggregate QC, resumable output. Yalnız gerçek ihtiyaç varsa yerel UI.

Kabul: iki bölüm üç dil aynı CLI ile işler; bir işin hatası doğru raporlanır ve başarılı işi bozmaz; dry-run ve cache invalidation anlaşılır; lisans/özel kayıt kuralları korunur. Yayın otomasyonu ayrı açık yetki olmadan eklenmez.

## Açık kararlar ve tamamlanma durumu

- Hedef Windows/RTX 5090 erişimi ve gerçek NVENC testi: bekliyor.
- Repository LICENSE seçimi ve exact Windows artifact/license manifesti: bekliyor.
- Üç dil ses kayıtları / placeholder kararı ve Mandarin insan incelemesi: bekliyor.
- Uygulama kodu, testler, model benchmark ve video çıktı: henüz yok.
- Bu görev: audit belgesi oluşturuldu, plan bu bulgulara göre güncellendi. Sonraki kodlama fazları otomatik başlatılmaz.
