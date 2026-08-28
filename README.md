# Uklanjanje šuma iz slika pomoću totalne varijacije

Ovaj projekat predstavlja studentsku implementaciju i komparativnu analizu savremenih metoda za restauraciju digitalnih slika, sa posebnim fokusom na Rudin-Osher-Fatemi (ROF) model i Chambolle-ov projekcioni algoritam za minimizaciju totalne varijacije (TV). Rezultati su upoređeni sa klasičnim linearnim (Gaussian) i nelinearnim (Median) filterima.


## 📋 Pregled projekta
Autorke projekta: **Natalija Filipović** i **Irina Marko**

Glavni cilj projekta je uklanjanje aditivnog Gausovog šuma iz slika uz očuvanje oštrine ivica i finih strukturnih detalja. Dok tradicionalni filteri često zamućuju prelaze između objekata, regularizacija totalne varijacije uspešno rešava ovaj problem tretiranjem slike kroz prostore funkcija ograničene varijacije.

U projektu su upoređene tri metode:

- **Gausov filter (Gaussian blur):** Linearni filter koji uklanja šum, ali istovremeno deluje kao niskopropusni filter, čime nepovratno zamućuje ivice.
- **Medijanski filter (Median filter):** Nelinearni filter, izuzetno efikasan za impulsni ("so i biber") šum, ali sa slabijim performansama na visokofrekventnom Gausovom šumu.
- **Chambolle TV algoritam:** Iterativna metoda koja minimizuje totalnu varijaciju slike. Uspešno uklanja sitne oscilacije (šum), dok velike skokove intenziteta (ivice) ostavlja netaknutim.

## 📐 Teorijska osnova i matematički model

### 1. ROF (Rudin-Osher-Fatemi) Model
Formulisan 1992. godine, ROF model posmatra problem uklanjanja šuma kao problem uslovne minimizacije. Za datu šumnu sliku $f$, traži se očišćena slika $u$ koja minimizuje sledeći funkcional energije:

$$\min_{u} E(u) = TV(u) + \frac{\lambda}{2} \Vert{}u - f\Vert{}_2^2$$

gde je:
- $TV(u) = \int_{\Omega} \vert{}\nabla u\vert{} dx$ član regularizacije koji kažnjava ukupne oscilacije (koristi $L^1$ normu gradijenta, što omogućava očuvanje oštrih skokova).
- $\Vert{}u - f\Vert{}_2^2$ član vernosti (fidelity term) koji sprečava da se očišćena slika previše udalji od originalne prljave slike.
- $\lambda$ (lambda) je težinski parametar (trade-off) koji kontroliše balans između glatkoće i vernosti originalu.

### 2. Chambolle-ov projekcioni algoritam (2004)
Direktna minimizacija ROF modela je numerički nestabilna na ravnim delovima slike (gde je $\nabla u \approx 0$, što dovodi do deljenja sa nulom). Chambolle je rešio ovaj problem uvođenjem dualnog problema.

Očišćena slika se dobija preko formule:

$$u = f - \lambda \, \text{div}(p)$$

gde dualno polje (strelice) $p = (p^1, p^2)$ zadovoljava uslov ograničenosti $\Vert{}p\Vert{} \le 1$. Polje $p$ se računa iterativno kroz fiksnu tačku:

$$p^{n+1} = \frac{p^n + \tau \nabla(\text{div}(p^n) - f/\lambda)}{1 + \tau \Vert{}\nabla(\text{div}(p^n) - f/\lambda)\Vert{}}$$

gde je $\tau \le 1/8$ korak gradijentnog spusta koji obezbeđuje konvergenciju algoritma.

## 📈 Evaluacija i rezultati
Kvalitet rekonstrukcije se meri pomoću tri standardne metrike u odnosu na originalnu (čistu) sliku:

- **MSE (Mean Squared Error):** Prosečna kvadratna greška. Cilj je postići što manju vrednost.
- **PSNR (Peak Signal-to-Noise Ratio):** Odnos vršnog signala i šuma izražen u decibelima (dB). Veća vrednost označava kvalitetniju i očuvaniju sliku.
- **SSIM (Structural Similarity Index Measure):** Indeks strukturne sličnosti koji meri očuvanje kontrasta, luminansu i strukturu (vrednosti od -1 do 1, gde je 1 savršeno poklapanje sa originalom).

**Ključni zaključci:**
- **Očuvanje ivica:** Chambolle TV algoritam postiže značajno viši PSNR i manji MSE u poređenju sa Gausovim filterom, jer ne zamućuje granice objekata.
- **Efekat stepenica (Staircase effect):** Kao teoretsko ograničenje TV regularizacije, na mestima gde slika ima blage prelaze (gradijente), algoritam teži da napravi ravne delove nalik stepenicama. Ova pojava je vizuelno dokumentovana u projektu.

## 🚀 Kako pokrenuti projekat

**Prerekviziti**
Preporučuje se kreiranje virtuelnog okruženja. Potrebne biblioteke možete instalirati pokretanjem:

```bash
pip install numpy scipy matplotlib scikit-image jupyter

jupyter notebook gaussian_median_chambolle.ipynb
```
## 📚 Literatura

1. Rudin, L. I., Osher, S., & Fatemi, E. (1992). **Nonlinear total variation based noise removal algorithms**. *Physica D: Nonlinear Phenomena*. [Pročitaj rad](https://web.eecs.utk.edu/~hqi/ece692/references/noise-TV-PhysicaD92.pdf)
2. Chambolle, A. (2004). **Chambolle’s Projection Algorithm for Total Variation Denoising** (IPOL Journal implementacija i analiza). [Pročitaj rad](https://www.ipol.im/pub/art/2013/61/article_lr.pdf)
