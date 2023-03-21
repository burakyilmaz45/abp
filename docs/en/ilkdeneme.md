
| Left columns  | Right columns |
| ------------- |:-------------:|
| left foo      | right foo     |
| left bar      | right bar     |
| left baz      | right baz     |
[Google araması için tıklayınız..](https://www.google.com.tr/?hl=tr)`


   <!DOCTYPE html>
<html>
  <head>
    <title>Giriş Ekranı</title>
    <script>
      function login() {
        var username = document.getElementById("username").value;
        var password = document.getElementById("password").value;
        
        if (username == "admin" && password == "admin123") {
          window.location.href = "Index.md";
        } else {
          alert("Hatalı kullanıcı adı veya şifre!");
        }
      }
    </script>
  </head>
  <body>
    <h2>Giriş Yap</h2>
    <form onsubmit="login(); return false;">
      <label for="username">Kullanıcı Adı:</label>
      <input type="text" id="username" name="username"><br><br>
      <label for="password">Şifre:</label>
      <input type="password" id="password" name="password"><br><br>
      <input type="submit" value="Giriş Yap">
    </form>
  </body>
</html>
