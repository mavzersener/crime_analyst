# Architecture Gap Analysis — Crime Analyst

Tarih: 2026-10-09. İncelenen kaynak: `main`, commit `714369880fc3a4f99445116e874091067343bf27`.
Kapsam: yalnız mimari ve planlama; uygulama kodu, kurulum, model indirme veya video üretimi yapılmadı.

## 1. Sonuç ve MVP kararı

İlk hedef Windows 11 Pro / RTX 5090 32 GB üzerinde Poisson(λ=3) için Türkçe, İngilizce ve standart Mandarin (basitleştirilmiş yazı, zh-CN) üç adet 45–60 saniyelik 1080×1920, 30 fps H.264/AAC MP4 üretmektir. Dil bağımsız tek sahne zaman çizelgesi; dil başına başlık, anlatım, UTF-8 SRT ve metadata gerekir. Veriler yalnız sentetik, tüm görseller açıkça varsayımsal etiketli olmalıdır.

Öneri: Python 3.12 x64 + NumPy/SciPy + Matplotlib Agg + FFmpeg subprocess. İlk MVP'de doğrudan yerel kaydedilmiş ses; yoksa görünür ve metadata içinde belirtilen yer tutucu ses. Klonlama MVP'nin ön koşulu değildir. Manim, Blender, web UI, LLM çeviri servisi ve otomatik yayın kapsam dışıdır. Matplotlib CPU'da kare üretir; RTX 5090 için GPU kullanımı FFmpeg h264_nvenc ile somut olarak doğrulanır. libx264 yalnız açık CPU geri dönüşüdür; bu çıktı GPU kabulünü karşılamaz.

**Doğrulama sınırı:** Repository ve aşağıdaki birincil kaynaklar incelendi. Windows makinesi, RTX 5090, sürücü, NVENC ve herhangi bir ses modeli bu oturumda erişilebilir değildir. Hedef uyumluluğu ve tam bağımlılık sürümleri henüz deneysel olarak doğrulanmış değildir; kurulum ve kodlama öncesi kapılar aşağıdadır.

## 2. Repository envanteri ve yeniden kullanım

Recursive GitHub tree eksiksizdi (truncated=false); altı dosyanın tamamı okundu. AGENTS.md, LICENSE, Python kodu, pyproject/requirements/lock, test, CI, model, kayıt ve hazır MP4 yok.

| Varlık | Değer | Boşluk / karar |
| --- | --- | --- |
| CODEX_HANDOVER_CRIME_ANALYST.md | Hedef, ses rızası ve planlama sınırı | Kabul ölçütlerinin kaynak belgesi |
| README.md | Yerel çalışma, üç dil, sentetik veri | Mevcut ürün değil, hedef tanımı |
| content/episodes/poisson_intro/README.md | Formül, λ=3, üç metin, 45–60 sn | Başlık, sahne sınırları, zamanlı altyazı, ses ve dil incelemesi eksik |
| docs/IMPLEMENTATION_PLAN.md | Başlangıçta beş genel faz | Küçük teslimatlar ve ölçülebilir kapılarla güncellenecek |
| web/index.html | Türkçe 26 aileli canvas öğrenme prototipi | Renderer veya bilimsel referans olarak kullanılmaz |
| .gitignore | voice/references, voice/checkpoints, outputs ve secrets koruması | Yeni yerel kayıt/cache yolları eklenirse kapsam tekrar kontrol edilmeli |

Web Poisson çizimi k/4 değerlerini yuvarlayarak aynı tam sayı PMF'sini tekrarlıyor, normalize edilmiş sürekli çizgi çiziyor; olasılık ekseni ve kesikli destek görünmüyor. Diğer ailelerin çoğu ortak prototip eğriyi kullanıyor. Tema ve pedagojik katalog fikri yeniden kullanılabilir; hesaplar Python bilimsel katmanda bağımsız doğrulanmalıdır. Web dosyası bu aşamada değişmez.

## 3. Ortam bulguları ve Windows bağımlılık kapısı

Git clone, mevcut shell'in proxy:8080 erişimi hatasıyla başarısız oldu. Kaynaklar GitHub bağlantısı üzerinden sabit commit ile okundu; geçmiş değiştirilmedi.

Gerçekte gözlenen oturum: Linux 6.18.44, Python 3.12.14, NumPy 2.3.5, SciPy 1.17.0, Matplotlib 3.10.8, FFmpeg/ffprobe 7.1.5. torch ve pytest paketleri metadata kontrolünde tespit edilmedi; nvidia-smi PATH üzerinde yok. Bunlar Windows kurulum önerisi veya GPU başarı kanıtı değildir. FFmpeg yapılandırmasında --enable-gpl ve --enable-libx264 var. Encoder varlığı dahi gerçek GPU kodlama başarısı değildir.

Hedef Windows'ta önce PowerShell ile şu kanıtlar toplanmalı:

- Get-ComputerInfo, nvidia-smi: OS, kart adı, VRAM, sürücü sürümü; CUDA gösterimi sürücünün desteklediği üst sınırdır, toolkit kurulumu değildir.
- py -0p, py -3.12 --version; x64 venv, pip check; uygun Windows wheel bulunması. Linux sürümleri otomatik kilitlenmez.
- Get-Command ffmpeg,ffprobe; ffmpeg -version, -buildconf, -encoders, -filters. h264_nvenc, libx264, native AAC ve subtitles/libass ayrı kontrol edilir.
- 2 saniye 1080×1920 30 fps test kaynağını h264_nvenc ile gerçekten kodla; ffprobe ile çözümle; sürücü/API hatasında GPU kapısı başarısız sayılır.
- Font dosyaları, lisans ve hash; ÇĞİÖŞÜ çğıöşü, λ, e, 泊松分布 ve bütün gerçek metinleri kareye basıp tofu/taşma kontrolü.
- Proje yolu ve boşluk/Unicode içeren Windows yolları; subprocess argüman listeleri, UTF-8 ve shell=False.
- Temiz venv kurulumundan sonra tam sürümleri, wheel/hash'leri, FFmpeg build kaynağını ve font hash'lerini kaydet. Kurulum öncesi hedef doğrulaması, uygulama öncesi mimari inceleme tamamlanmalı.

CUDA/PyTorch çekirdek video MVP'si için gerekli değildir. Sonraki TTS fazında PyTorch 2.7.0 birincil release notları Blackwell ve CUDA 12.8 desteğini belgeliyor; bu eski herhangi bir CUDA wheel'inin RTX 5090'da çalışacağı anlamına gelmez. Seçilen güncel Windows wheel/model kombinasyonu sm_120 uyumluluğu, gerçek CUDA tensor işlemi, model çıkarımı ve VRAM ölçümüyle doğrulanır. FlashAttention/derlenmiş uzantılar zorunlu yapılmaz; gerekirse yalnız ayrı TTS ortamında WSL2 değerlendirilir.

## 4. Önerilen modül sınırları ve veri sözleşmeleri

Diskteki dosyalar arası akış: episode → validation/stats → master timeline → shared chart frames → language overlays + audio + captions → FFmpeg → QC report. Ağ çağrısı gerektirmez.

| Modül | Sorumluluk / çıktı |
| --- | --- |
| content/episodes | Sürüm kontrollü episode JSON, onaylı metinler ve storyboard |
| stats | PMF, bağımsız referans kontrolleri, parametre doğrulama |
| data/synthetic | Sabit seed ve RNG algoritmasıyla üretilen örnekler, manifest |
| visuals | Agg kareler, integer support bar/stem chart, ortak sahne kimlikleri |
| localization | tr/en/zh-CN metin, terimler, font ve satır kırılması |
| voice | Recorded/placeholder, daha sonra izole TTS adapter |
| media | Overlay, SRT, audio normalizasyonu ve H.264/AAC mux |
| quality | Sayısal, metin, süre/stream/QC kontrolleri |
| cli / tests | Tek giriş noktası, hata kodları ve bağımsız sınamalar |

Episode JSON önerisi: schema_version, episode_id, hypothetical=true, distribution, lambda=3, unit=events/month, seed, sample_size, rng, duration_ms, width=1080, height=1920, fps=30, scenes[{id,start_ms,end_ms,visual}], locales{tr,en,zh-CN:{title,narration,cues[{scene_id,start_ms,end_ms,text}],font}}, audio_mode, audio_paths. Bu bir öneridir; mevcut dosya değildir. Gizli kayıtlar ve rıza belgeleri repo dışında/ignored yerel klasörde kalır; metadata özel mutlak yolları içermez.

Bütün diller aynı sahne sınırlarını kullanır. Ses sahne bazında kaydedilir; sahne süresi en uzun onaylı dil segmenti ve kısa tamponla belirlenir. 60 saniye aşılırsa metin/sahne süreleri üç dil için birlikte düzeltilir; konuşma kesilmez veya kontrolsüz hızlandırılmaz. Kısa segmentler sessiz tamponla hizalanır. SRT karakter sayısından otomatik süre tahmini kabul edilmez; onaylı segment zamanları kullanılır.

Önerilen CLI (henüz uygulanmadı):
`python -m crime_analyst doctor --encoder h264_nvenc`
`python -m crime_analyst validate --episode content/episodes/poisson_intro/episode.json`
`python -m crime_analyst render --episode ... --languages tr en zh-CN --audio-mode recorded --encoder h264_nvenc`
`python -m crime_analyst verify --run outputs/<run-id>`
dry-run dosya, lisans ve araçları kontrol eder; render başlatmaz. Eksik locale/font, hatalı λ, okunamayan ses, başarısız encoder veya QC için sıfır olmayan çıkış kodu; sessiz fallback yok. İlk fazda HTTP API gerekmez.

Çıktılar: outputs/<run-id>/{tr,en,zh-CN}.mp4, .srt, locale metadata; run-manifest.json, qc-report.json ve ortak timeline. Manifest kaynak commit, script/input hash, seed/RNG, sürümler, font, encoder, süre ve audio_mode içerir. Aynı girdiler sayısal veriyi ve sahne zamanlarını yeniden üretir; MP4 byte-identical olma garantisi verilmez.

## 5. Bilimsel doğruluk, dil ve kalite

Poisson support k=0,1,...; λ pozitif sonlu (pilot 3). P(0)=exp(-3)=0.04978706836786394, yaklaşık %4,98. SciPy poisson.pmf ile math.exp/factorial bağımsız hesap karşılaştırılır (mutlak tolerans 1e-12). Gösterilen kesikli aralık için kalan kuyruk kütlesi açıklanır; örnek histogramı teorik PMF'den ayrı etiketlenir. Sabit seed teorik olasılığı tahmin etmek için kullanılmaz.

Metinlerde varsayımsal mahalle örneği açık belirtilmeli; sabit oran ve bağımsız artış varsayımları anlatılmalı; suç tahmini veya nedensellik iddiası yok. Türkçe 'Poisson dağılımı', English 'Poisson distribution', Mandarin '泊松分布' tutarlı kullanılmalı. Mandarin için yetkin insan incelemesi, sayı/formül telaffuzu ve okunabilirlik kapıdır; metinlerin varlığı inceleme tamamlandı anlamına gelmez.

Önerilen kabul sınamaları: doğru/yanlış λ ve support; PMF/kuyruk; sabit seed; eksik locale; non-overlapping monotonic cues; sahne sınırları üç dilde aynı; font glyph ve gerçek frame görsel incelemesi; Windows Unicode paths; ses bitişi ilgili sahne sınırında; boş/eksik ses; encoder hatası; ffprobe stream/codec/dimensions/fps. Final süre 45–60 saniye, üç dil süre farkı ve ses/video bitiş farkı en fazla bir kare + AAC bir paket süresi; konuşma/altyazı hizası hedef en fazla 100 ms ve insan izlemesi. SRT ayrı UTF-8 teslim edilir; final overlay veya burned captions ile görünürlük sağlanır.

İkinci bölüm ertelenir: NB2 için μ=3, α>0, Var=μ+αμ²; SciPy n=1/α ve p=n/(n+μ). Aynı mean, farklı variance ve α→0 Poisson limiti bağımsız kontrol edilir.

## 6. Bağımlılık ve lisans değerlendirmesi

Birincil kaynak incelemesi: 2026-10-09. Aşağıdaki bağlantılar upstream durumunu gösterir; final sürüm/commit ve indirilen artifact lisansı ayrıca sabitlenmeli. Kod lisansı, ağırlık lisansı, font, FFmpeg build ve üretilen medya hakları birbirinin yerine geçmez.

| Bileşen | Kaynak bulgusu / ticari değerlendirme | Karar |
| --- | --- | --- |
| Python | PSF lisansı; dağıtılan kurulumun üçüncü taraf notices'ı da tutulmalı | Windows artifact lisansı hedefte doğrulanacak |
| NumPy | BSD-3-Clause, notices ve endorsement koşulları | Çekirdek, release artifact lisansları saklanmalı |
| SciPy | BSD-3-Clause, aynı yükümlülükler | Çekirdek, wheel'in bundled libs koşulları ayrıca |
| Matplotlib | Matplotlib lisansı ticari kullanım/dağıtıma izin verir; copyright/license ve değişiklik açıklaması | Çekirdek; font ve yardımcı paketler ayrı |
| FFmpeg | Varsayılan LGPL-2.1+, GPL seçenekleri build lisansını değiştirir; nonfree kombinasyonlar dağıtılamayabilir | Buildconf/source kaydı; GPL build dağıtımında kaynak/notice yükümlülükleri |
| libx264 / NVENC / AAC | libx264 GPL build'e etki eder; NVIDIA bileşenleri ve codec patentleri ayrı değerlendirme | Runtime kullanımı ile binary dağıtımı ayrılır; toplu codec hakları onaylanmış sayılmaz |
| Noto Sans CJK | SIL OFL 1.1: kullanım/embedding mümkün, font dağıtımında lisans/notices, tek başına satış yasağı | Chinese font adayı; Latin fontun kendi artifact lisansı da kontrol edilmeli |
| pytest ve transitif paketler | Henüz kurulu veya kilitli değil | Exact artifact lisans envanteri kurulum fazında; core minimum tutulmalı |
| Repository | LICENSE yok | Proje lisansı sahibin seçimini bekler; başkalarına açık kaynak izinleri varsayılmaz |

Ses adayları (model kurulumu veya benchmark yapılmadı):

| Aday | TR / EN / ZH kanıtı | Kod / ağırlık ve ticari kapı | MVP kararı |
| --- | --- | --- | --- |
| Doğrudan kayıt | Üç dil için sağlanacak sesler henüz yok | Kullanıcıya ait/izinli kayıt ve açık kullanım rızası | Öncelikli, klon yok |
| Yer tutucu | Konuşma doğruluğu sağlamaz | Basit üretilmiş sessizlik/test tonu; görünür etiket | Teknik pilot kabulü, bitmiş anlatımlı ders sayılmaz |
| XTTS-v2 | Upstream config tr/en/zh-cn listeliyor | Model registry CPML ve tos_required=true; kod lisansı ağırlıkları kapsamaz. Tam güncel CPML ve olası ticari izin bu oturumda doğrulanmadı | Ticari klonlama kapalı; lisans/izin, hedef benchmark ve dinleme onayı gerek |
| MeloTTS | EN/Chinese var; TR belgelenmiyor | README kodu MIT, ticari kullanım diyor; checkpoint şartları ayrıca incelenmeli | Üç dil tek engine hedefini karşılamıyor |
| F5-TTS | TR/EN/ZH birlikte doğrulanmadı | README: kod MIT; pretrained model CC-BY-NC | Ticari pretrained akış için elenir |
| Qwen3-TTS | 10 dil listesinde EN/Chinese var, Turkish yok | Seçilecek kod ve checkpoint lisansı ayrıca kontrol edilmeli; burada ticari ağırlık onayı verilmedi | Üç dil tek engine hedefini karşılamıyor |

Klonlama ancak kullanıcıya ait yerel kayıt, açık rıza, exact checkpoint lisansı, üç dil resmi destek kanıtı, Windows/GPU benchmark, telaffuz ve kimlik dinleme onayı ile açılır. Kişisel ses GitHub'dan indirilmez veya commit edilmez. Hiçbir aday bu oturumda tüm kapıları geçmiş değildir.

Birincil kaynaklar:

- [NumPy LICENSE](https://github.com/numpy/numpy/blob/main/LICENSE.txt)
- [SciPy LICENSE](https://github.com/scipy/scipy/blob/main/LICENSE.txt)
- [Matplotlib LICENSE](https://github.com/matplotlib/matplotlib/blob/main/LICENSE/LICENSE)
- [FFmpeg LICENSE](https://github.com/FFmpeg/FFmpeg/blob/master/LICENSE.md)
- [Noto CJK OFL](https://github.com/notofonts/noto-cjk/blob/main/Sans/LICENSE)
- [PyTorch 2.7.0 Blackwell release](https://github.com/pytorch/pytorch/releases/tag/v2.7.0)
- [XTTS language config](https://github.com/coqui-ai/TTS/blob/dev/TTS/tts/configs/xtts_config.py)
- [XTTS model registry / CPML](https://github.com/coqui-ai/TTS/blob/dev/TTS/.models.json)
- [MeloTTS README](https://github.com/myshell-ai/MeloTTS/blob/main/README.md)
- [F5-TTS README / weight restriction](https://github.com/SWivid/F5-TTS/blob/main/README.md)
- [Qwen3-TTS README / languages](https://github.com/QwenLM/Qwen3-TTS/blob/main/README.md)

## 7. Açık kapılar ve öncelik

P0: hedef Windows/GPU erişimi ve NVENC kanıtı; exact dependency/build/font lisans envanteri; repo lisansı kararı; üç dil içerik/özellikle Mandarin incelemesi. Bu belge bunları çözülmüş göstermiyor.
P1: episode/timeline sözleşmesi, sayısal testler, kayıt veya açık yer tutucu seçimi, renderer ve QC.
P2: lisans/rıza/benchmark ile TTS; NB2; batch ve yalnız gerekirse UI.
Kodlama için ayrı yetki henüz yok; bu görev yalnız belgeleri üretir. Yayınlama veya özel kayıt yükleme yetkisi yok.
