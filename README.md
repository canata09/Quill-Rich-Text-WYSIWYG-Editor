Quill kullanarak yazılan yazıları veritabanına kaydetmek için aşağıdaki adımları izleyebilirsiniz. Bu süreç, HTML, JavaScript ve PHP kullanarak Quill editöründe yazılan içerikleri alıp, bu veriyi bir veritabanına (örneğin MySQL) kaydetmeyi içerir.

### 1. **HTML Yapısı ve Quill Entegrasyonu**

İlk olarak, Quill'i sayfanıza entegre etmelisiniz.

#### HTML

```html
<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quill Editor</title>
    <link href="https://cdn.quilljs.com/1.3.6/quill.snow.css" rel="stylesheet">
</head>
<body>
    <form action="save.php" method="POST">
        <div id="editor-container"></div> <!-- Quill Editörü buraya yerleşecek -->
        <input type="hidden" name="content" id="editor-content"> <!-- Quill içeriğini buraya kaydedeceğiz -->
        <button type="submit">Kaydet</button>
    </form>

    <script src="https://cdn.quilljs.com/1.3.6/quill.min.js"></script>
    <script>
        // Quill editörünü başlat
        var quill = new Quill('#editor-container', {
            theme: 'snow',
            placeholder: 'Metninizi buraya yazın...',
            modules: {
                toolbar: [
                    [{ 'header': '1' }, { 'header': '2' }, { 'font': [] }],
                    [{ 'list': 'ordered'}, { 'list': 'bullet' }],
                    ['bold', 'italic', 'underline'],
                    [{ 'align': [] }],
                    ['link', 'image']
                ]
            }
        });

        // Form submit öncesinde Quill içeriğini hidden input'a aktar
        document.querySelector('form').onsubmit = function() {
            var editorContent = quill.root.innerHTML; // Quill içeriğini al
            document.querySelector('#editor-content').value = editorContent; // Hidden input'a ekle
        };
    </script>
</body>
</html>
```

Bu örnekte:

- Quill editörü, `#editor-container` elementine yerleştirildi.
- Formdaki `textarea` yerine `input` türünde bir hidden field (`#editor-content`) kullanarak Quill içeriği form verisi olarak gönderilecek.
- Form gönderildiğinde Quill editöründeki içerik, hidden input'a aktarılır ve PHP'ye gönderilir.

### 2. **PHP: Veritabanına Kayıt**

Quill'den gelen HTML içeriğini PHP ile alıp veritabanına kaydedeceğiz. Bu örnekte MySQL kullanacağız.

#### PHP: `save.php`

```php
<?php
// Veritabanı bağlantısı için gerekli bilgiler
$host = 'localhost';
$dbname = 'veritabani_adiniz';
$username = 'kullanici_adiniz';
$password = 'parolaniz';

try {
    $pdo = new PDO("mysql:host=$host;dbname=$dbname", $username, $password);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

    // Formdan gelen içerik
    if ($_SERVER['REQUEST_METHOD'] === 'POST') {
        $content = $_POST['content'];

        // Veritabanına kaydetme
        $stmt = $pdo->prepare("INSERT INTO posts (content) VALUES (:content)");
        $stmt->bindParam(':content', $content);
        $stmt->execute();

        echo "İçeriğiniz başarıyla kaydedildi!";
    }
} catch (PDOException $e) {
    echo "Veritabanı hatası: " . $e->getMessage();
}
?>
```

#### Veritabanı Yapısı

Bu örnekte, veritabanında aşağıdaki gibi bir tablo kullanıyoruz:

```sql
CREATE TABLE posts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    content TEXT NOT NULL
);
```

### 3. **Açıklamalar:**

- **Quill İçeriği**: Quill editöründe yazdığınız metin, HTML formatında `innerHTML` özelliği ile alınır. Bu içerik, form gönderildiğinde `textarea` yerine kullanılan `hidden input` aracılığıyla PHP'ye gönderilir.
  
- **PHP'de Veritabanı İşlemleri**: PHP'de PDO (PHP Data Objects) kullanarak MySQL veritabanına bağlantı sağlıyoruz ve gelen `POST` verisini `content` alanına kaydediyoruz. Bu veriyi doğrudan veritabanına kaydederken HTML içeriğini sakladığımız için veritabanında metin, HTML biçiminde kaydedilir.

- **Veritabanına HTML Kayıt Edilmesi**: Quill editörü, zengin metin (HTML) içerik sağladığı için, bu içerik veritabanında HTML formatında saklanır. Yani, yazdığınız metin bold, italic, link gibi stil bilgileriyle birlikte kaydedilir.

### 4. **Veritabanından Veriyi Çekme ve Görüntüleme**

Veritabanına kaydedilen içeriği PHP ile ekrana yazdırmak için aşağıdaki gibi bir işlem yapabilirsiniz:

```php
<?php
// Veritabanı bağlantısını yapıyoruz
$pdo = new PDO("mysql:host=$host;dbname=$dbname", $username, $password);
$pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

// Son kaydedilen içeriği çekme
$stmt = $pdo->query("SELECT content FROM posts ORDER BY id DESC LIMIT 1");
$row = $stmt->fetch(PDO::FETCH_ASSOC);

echo "<div>" . $row['content'] . "</div>"; // Veritabanındaki HTML içeriği doğrudan yazdırıyoruz
?>
```

Bu kod, veritabanındaki son kaydedilen içeriği alır ve doğrudan HTML olarak görüntüler.

---

### Özet

- Quill editörü ile zengin metin düzenlemesi yapabilirsiniz.
- Quill editöründeki içeriği HTML olarak alıp PHP'ye göndererek veritabanına kaydedebilirsiniz.
- Veritabanına kaydedilen HTML içeriği, kullanıcıya tekrar gösterilebilir.
- PHP ve MySQL kullanarak bu işlemi gerçekleştirebilir, zengin metin içeriğini veritabanına güvenli bir şekilde kaydedebilirsiniz.

Bu yöntem, genellikle blog, içerik yönetim sistemleri (CMS) ve benzeri projelerde kullanılır.
