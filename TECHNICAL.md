# Sosyal - Detaylı Teknik Dokümantasyon

## 1. Proje Özeti
Bu proje, kullanıcıların fotoğraf paylaşabildiği, diğer kullanıcıları takip edebildiği ve iletişim formları aracılığıyla mesaj gönderebildiği dinamik bir sosyal medya web uygulamasıdır. Proje, Node.js tabanlı Express framework'ü ile geliştirilmiş olup şablon motoru olarak EJS (Embedded JavaScript) kullanmaktadır.

## 2. Teknoloji Yığını (Tech Stack)
* **Backend:** Node.js, Express.js
* **Veritabanı:** MongoDB (Mongoose ODM)
* **Şablon Motoru (Frontend):** EJS
* **Stil Yönetimi:** Vanilla CSS (Public klasörü altında)
* **Kimlik Doğrulama:** JSON Web Token (JWT) ve Cookie-Parser, Bcrypt.js (şifre hashleme)
* **Dosya Yükleme:** express-fileupload, Cloudinary (Bulut Depolama)
* **Mail Servisi:** Nodemailer
* **HTTP İstek Yönetimi:** Method-Override (PUT, DELETE gibi metodları HTML formlarında kullanmak için)
* **Güvenlik / Validasyon:** validator

## 3. Mimari Yapı (MVC - Model, View, Controller)
Proje, katmanlı bir **MVC (Model-View-Controller)** yapısı üzerine inşa edilmiştir:
* **Model (`models/`):** Veritabanı şemalarını ve veri ile etkileşimi yönetir. Mongoose kullanılarak MongoDB ile haberleşir.
* **View (`views/`):** EJS dosyalarını içerir. Kullanıcıya gösterilen arayüz (UI) bu katmanda işlenir.
* **Controller (`controllers/`):** İstemciden gelen istekleri (request) alır, gerekli modellerle iş mantığını çalıştırır ve uygun view'i render eder veya JSON yanıtı döner.
* **Routes (`routes/`):** Express Router yapısı ile İstek (request) URL'lerini ilgili controller fonksiyonlarına yönlendirir.

## 4. Klasör Yapısı
```text
sosyal/
├── api/                  # Vercel vb. serverless deployment için API konfigürasyonları
├── controllers/          # İş mantığının yer aldığı denetleyici fonksiyonlar
├── middlewares/          # Ara katman yazılımları (Kimlik doğrulama, res.locals vb.)
├── models/               # Mongoose veritabanı şemaları (User, Photo, Message)
├── public/               # Statik dosyalar (CSS, JS, Görseller)
├── routes/               # Express yönlendirmeleri (API ve sayfa yolları)
├── views/                # EJS şablonları (partials, sayfalar)
├── .env                  # Çevresel değişkenler (Gizli anahtarlar, DB URI)
├── app.js                # Express uygulamasının yapılandırması ve middleware'leri
├── db.js                 # MongoDB bağlantı konfigürasyonu
├── server.js             # Uygulamayı ayağa kaldıran ana giriş noktası
└── package.json          # Proje bağımlılıkları ve scriptleri
```

## 5. Kurulum ve Çalıştırma

Projeyi yerelde (local ortamda) çalıştırmak için aşağıdaki adımları izleyin:

1. **Bağımlılıkları Yükleyin:**
   ```bash
   npm install
   ```
2. **Ortam Değişkenlerini Ayarlayın:** Proje kök dizininde bir `.env` dosyası oluşturun (Bkz. Bölüm 6).
3. **Geliştirme Modunda Başlatın (Nodemon ile):**
   ```bash
   npm run dev
   ```
   *Veya standart başlatma için:* `npm start`
4. Uygulama, `.env` dosyasında belirtilen porta (genellikle `localhost:3000`) başarıyla kalkacaktır.

## 6. Ortam Değişkenleri (.env)
Projenin sorunsuz çalışması için kök dizinde yer alması gereken `.env` yapılandırması:

```env
# Sunucu ve Veritabanı
PORT=3000
DB_URL=mongodb+srv://<kullanici_adi>:<sifre>@<cluster>.mongodb.net/<veritabani_adi>

# Güvenlik ve Kimlik Doğrulama
JWT_SECRET=super_gizli_jwt_anahtari

# Cloudinary (Fotoğraf Yükleme İçin)
CLOUD_NAME=cloud_isminiz
CLOUD_API_KEY=cloud_api_anahtariniz
CLOUD_API_SECRET=cloud_api_gizli_anahtariniz

# Nodemailer (İletişim Formu Mail Gönderimi İçin)
NODE_MAIL=gonderici_mail_adresiniz@gmail.com
NODE_PASS=uygulama_sifreniz (App Password)
```

## 7. Veritabanı Şemaları (Models)

### 7.1. User Model (`userModel.js`)
Kullanıcı kayıt bilgilerini, takipçi ve takip edilen listelerini yönetir.
* `username`: (String, benzersiz, zorunlu)
* `email`: (String, benzersiz, zorunlu)
* `password`: (String, zorunlu, bcrypt ile hashlenir)
* `followers`: (User referansları dizisi - Array of ObjectIds)
* `followings`: (User referansları dizisi - Array of ObjectIds)

### 7.2. Photo Model (`photoModel.js`)
Sistemde paylaşılan fotoğrafları barındırır.
* `name`: Fotoğraf başlığı (String, zorunlu)
* `description`: Fotoğraf açıklaması (String, zorunlu)
* `uploadedAt`: Yüklenme tarihi (Date, varsayılan olarak şu anki zaman)
* `image_id`: Cloudinary üzerindeki public ID (String)
* `url`: Cloudinary görsel URL'i (String)
* `user`: Yükleyen kullanıcının referansı (User ObjectID)

### 7.3. Message Model (`messageModel.js`)
İletişim formu (Contact Us) üzerinden gönderilen mesajları depolar.
* `name`: Gönderici adı (String)
* `email`: Gönderici e-posta adresi (String)
* `message`: Mesajın tam içeriği (String)

## 8. Routing ve API Uç Noktaları

Uygulamanın ana route kurgusu `app.js` üzerinden Express Router'lara bağlanmıştır:
* `/` (`pageRoute.js`): İndex, Hakkımızda (About), Register ve Login arayüzlerinin render edilmesi.
* `/photos` (`photoRoute.js`): Fotoğraf oluşturma, listeleme, tekil detay sayfası, silme ve güncelleme işlemleri.
* `/users` (`userRoute.js`): Kayıt olma, sisteme giriş yapma (login), çıkış yapma (logout), kullanıcı profilleri, takip etme (follow) ve takipten çıkma (unfollow) işlemleri.
* `/messages` (`messagesRoute.js`): İletişim sayfasından veritabanına mesaj kaydı.

## 9. Güvenlik ve Yetkilendirme
* **Kimlik Doğrulama (JWT):** Başarılı kullanıcı girişi yapıldığında, sunucu bir JSON Web Token (JWT) oluşturur ve bunu istemcinin tarayıcısına bir **Cookie** olarak kaydeder (`cookie-parser` ile yönetilir).
* **Ara Katman (Middleware - `authMiddleware.js`):** EJS şablonlarında navigasyon barının (Login/Logout butonları) dinamik değişimi için her istekte kullanıcıyı doğrulayıp `res.locals.user` değişkenine atar. Korumalı rotalar için ek `authenticateToken` middleware'leri ile route bazlı yetki kontrolü yapılır.
* **Şifreleme (Bcrypt.js):** Kullanıcı şifreleri veritabanına açık metin (plaintext) olarak kaydedilmez. Mongoose `pre('save')` kancası (hook) kullanılarak kaydedilmeden önce `bcrypt` algoritması ile şifrelenir.

## 10. Dış Servis Entegrasyonları
* **Cloudinary:** Kullanıcı bir fotoğraf yüklediğinde, express-fileupload paketi sayesinde görsel önce sunucunun `/tmp` klasörüne (temp file) aktarılır. Ardından, backend üzerinden Cloudinary API'sine upload işlemi gerçekleştirilir. Başarılı yükleme sonrasında alınan URL ve ID bilgileri DB'ye kaydedilir.
* **Nodemailer:** Kullanıcı `Contact` (İletişim) formunu doldurduğunda, veritabanına kaydın yanı sıra Nodemailer kütüphanesi ve SMTP protokolü kullanılarak yetkili bir adrese bilgilendirme e-postası (notification email) gönderilir.


indirilen paketler:
npm init
npm install express
npm install -D nodemon
npm i mongoose
npm i ejs
npm i dotenv
npm i cookie -parser
npm i bcryptjs
npm i validator
npm i jsonwebtoken
npm i cloudinary
npm i express-fileupload
npm i method-override
npm i nodemailler
npm install method-override
npm i multer
npm i path

1-Kurulumlar,
Template kurulumu
npm init -y
npm i ejs
Nodemon paket kurulumu
Express paket kurulumu
Github Repo 
2- Template dosyalarının ayarlanması,
Static dosyalar
Views dosyalar
Partials dosyalar
3- Veritabanı bağlantıları,
Veritabanı bağlantısı
MongoDb
ENV
Mongoose paket kurulumu
4-MVC Yapısı,
Model
View
Controller
5- Model oluşturma,
Mongoose
Schema Yapısı
6- Thunder Client extension
Thunder client kurulumu
collection,request, reponse
Photolar için controller route ve app .js dosyalarında düzenleme
modeller yardımıyla yeni fotoğraflar eklenmesi sağlandı mongodb den kontrol edildi. thunder clienttan istekler gönderildi.
7-photoların listelenmesi ve sıralanması
dinamik photolar eklenmesi name ve descriptionların eklenmesi
menü tıklanmasında hangi menüdeysek hover olması controllera link eklenmesi ve activelik durumunun güncellenmesi
8-photo sayfasının oluşturulması
9- register sayfasının oluşturulması
10- kayıt işlemlerinde şifre gizleme,
Bcrypt JS kurulumu
password şifreleme
login sayfasının  oluşturulması
11- Kullanıcı yetkileri,
Authentication, Authorization, JSON web token jwt
12- Token kayıt,
cookie parser kurulumu
13- Kayıtlı kullanıcı için dinamik sayfa görünümü
14- Validation kavramı, validator kurulumu, Register uyarıları
15- Fotoğraf ve kullanıcı ilişkisi, Kullanıcının fotoğraf eklemesi
16- Görsel yükleme, cloudinary platformu ilişkisi, cloudinary kurulumu , express file upload paketi kurulumu
17- Kullanıcıların ve Profil sayfalarının oluşturulması
18- bugların giderilmesi, follow,followers
19- follow-followers-unfollow, method-override kurulumu
20- Photo delete işlemi
21- photo update işlemleri
22- İletişim sayfaları, nodemailler kurulumu
23- Bugların giderilmesi
24- Read me update
25- Profil fotoğrafı güncelleme
26- css dosyaları , header footer index about sayfalarında düzenlemeler

.env dosyasında olması gerekenler:
// mongodb için;
DB_URL=
PORT=
//json web token için;
JWT_SECRET = 
//cloudinart için ;
CLOUD_NAME=
CLOUD_API_KEY=
CLOUD_API_SECRET=
İletişim sayfası maili için ;
NODE_MAIL= 
NODE_PASS= 
