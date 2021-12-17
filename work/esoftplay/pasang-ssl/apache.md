# Cara Pasang SSL di server Apache - Esoftplay

1. Masuk ke layanan configure
   Isi form dari [https://www.digicert.com/easy-csr/openssl.htm]() untuk membuat command untuk membuat key dan csr.
   Atau anda bisa langsung menjalankan command.

   ``` sh
   openssl req -new -newkey rsa:2048 -nodes -keyout visilink.id.key -out visilink.id.csr
   ```

2. Paste ke .conf/ssl crt dan key rename dan ubah hak akses sesuai user yang digunakan misal `client`

3. Pada pemilihan verifikasi pilih HTTP.
   Biasanya anda akan disuruh untuk membuat file tertentu yang bisa diakses publik seperti ini:
   ```
   .well-known/pki-validation/F7D287606D645B24CA86996FCC0237D7.txt
   ```

3. Tambah konfigurasi pada file `.conf/erp.kamerapengintai.com.conf`.
   ``` conf
   <VirtualHost *:443>
     ServerName erp.kamerapengintai.com
     ServerAlias  *.erp.kamerapengintai.com
     DocumentRoot /home/client/domain/erp.kamerapengintai.com
     ErrorLog logs/erp.kamerapengintai.com-error_log
   
     SSLEngine on
     SSLProtocol all -SSLv2 -SSLv3
     SSLCertificateFile /home/client/.conf/ssl/erp.kamerapengintai.com.crt
     SSLCertificateKeyFile /home/client/.conf/ssl/erp.kamerapengintai.com.key
     SSLCertificateChainFile /home/client/.conf/ssl/erp.kamerapengintai.com.ca.crt # Gabungan dari _root dan _intermediate jika ada format spt ini
   </VirtualHost>
   ```

4. Periksa konfigurasi Apache dengan perintah `apachectl configtest` pastikan tidak ada konfigurasi yang eror.

5. Jika sudah anda bisa merestart server anda dengan perintah `rs` atau `systemctl restart httpd`.

### Side Note
- `aa` kanggo ngecek ana sing ngakses tah ora
- redirect http ke https
  Di file `config.php` tambakan:
  ``` php
  if (!empty($_SERVER['HTTP_HOST']) && !isset($_SERVER['HTTPS'])) {
      header('Location: https://'.$_SERVER['HTTP_HOST'].$_SERVER['REQUEST_URI']);
	  die();
  }
  ```

