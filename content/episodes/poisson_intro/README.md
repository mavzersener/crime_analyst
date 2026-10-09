# Episode 01 — Poisson | Can mathematics model crime counts?

**All figures are hypothetical.** This is an educational distribution example, not a crime forecast.

- Formula: P(X=k)=exp(-λ) λ^k/k! for nonnegative integers k.
- Example λ=3 incidents/month, P(X=0)=exp(-3)≈0.049787 (≈4.98%).
- Caveat: stationary rate and conditional independence assumptions can be inappropriate for empirical crime observations.
- Output languages: Turkish, English, Simplified Chinese / standard Mandarin.
- Target video: 45–60 seconds, 9:16, 1080×1920.
- Related follow-up: Negative Binomial overdispersion.

## Turkish narration
Bir mahallede ayda ortalama üç hırsızlık oluyorsa gelecek ay hiç hırsızlık kaydedilmeme olasılığı nedir? Poisson dağılımıyla, ortalamanın sabit olduğunu varsayarsak bunu hesaplayabiliriz. Sıfır olayın olasılığı e üzeri eksi üç, yaklaşık yüzde 4,98'dir. Ancak gerçek suç verilerinde olaylar zamana ve mekâna göre değişebilir. Bu nedenle Poisson uygunluğunu test etmeden sonuçları kullanmamalıyız.

## English narration
If a neighborhood records an average of three burglaries a month, what's the probability of recording none next month? Under a Poisson model with a constant rate, it is e to the power of negative three: about 4.98 percent. But real incident rates can change across space and time. Always test the model's assumptions before relying on its predictions.

## Mandarin narration / 简体中文
如果某个社区平均每个月记录三起入室盗窃案件，那么下个月一起也没有的概率是多少？假设事件发生率保持稳定，我们可以使用泊松分布进行计算。零起事件的概率是 e 的负三次方，大约为百分之四点九八。但真实的案件发生率可能随时间和地点发生变化，因此在应用结果之前，必须检验模型假设。
