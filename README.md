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
