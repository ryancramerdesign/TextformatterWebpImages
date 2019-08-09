# ProcessWire Textformatter module for WEBP Images

This module looks for references to JPG or PNG images in any formatted values you've selected
it for and updates them to be WEBP images. This is intended primarily for sites that are using
images in CKEditor textarea fields, as it updates any `<img>` tags pointing to JPG/PNG images
on-the-fly, automatically creating the WEBP images when they don't exist. Because it does this
as a Textformatter process, it applies to any existing pages automatically. 


## Requirements 

- This module requires ProcessWire 3.0.132 or newer since WEBP support was added by that 
  version of ProcessWire. It was developed with 3.0.137, so using that version or newer
  would be even better. 

- Your server must support WEBP either with GD or IMagick. 
  [See here](https://processwire.com/blog/posts/webp-images-in-pw/) for more details.
  
- I also recommend that you have fallback code in your .htaccess file so that clients that 
  do not support WEBP will automatically fallback to the JPG/PNG version (details further 
  below).

## Installation

1. Copy the files for this module (or just the .module file) into new directory:
   `/site/modules/TextformatterWebpImages/`
   
2. In your ProcessWire admin, click Modules > Refresh, and you'll see this module on
   the Site tab of your modules. Click the install button.    

3. In Setup > Fields, locate any Textarea fields where you want to automatically convert
   images to WEBP. This would typically be your CKEditor fields, like “body”. Edit those
   fields, click the “Details” tab, and select “Convert JPG/PNG images to WEBP” as one of
   the Text Formatters. Save. 
   
4. View a page that has one of the fields you just updated on it, and also has images 
   that would be converted to WEBP. Make sure everything is working as you expect. Note
   That the first time (only) viewing such a page may slow down the page render since it 
   has to generate the WEBP image   
   

## .htaccess fallback to JPG/PNG 

The following .htaccess rules are useful if not all clients to the website support WEBP,
enabling the original JPG/PNG files to be delivered when that is the case. This can go
after the `RewriteEngine On` line in your .htacess file. 

~~~~~~
# fallback to JPG file when WEBP not supported or does not exist
RewriteCond %{HTTP_ACCEPT} !image/webp [OR]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{DOCUMENT_ROOT}/$1$2$3/$4.jpg -f
RewriteRule ^(.*?)(site/assets/files/)([0-9]+)/(.*)\.webp(.*)$ /$1$2$3/$4.jpg [R=307,L]

# fallback to JPEG file when WEBP not supported or does not exist
RewriteCond %{HTTP_ACCEPT} !image/webp [OR]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{DOCUMENT_ROOT}/$1$2$3/$4.jpeg -f
RewriteRule ^(.*?)(site/assets/files/)([0-9]+)/(.*)\.webp(.*)$ /$1$2$3/$4.jpeg [R=307,L]

# fallback to PNG file when WEBP not supported or does not exist
RewriteCond %{HTTP_ACCEPT} !image/webp [OR]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{DOCUMENT_ROOT}/$1$2$3/$4.png -f
RewriteRule ^(.*?)(site/assets/files/)([0-9]+)/(.*)\.webp(.*)$ /$1$2$3/$4.png [R=307,L]
~~~~~~
Note that if you are using `$config->pagefileExtendedPaths = true;` in your 
/site/config.php file, the above rewrite rules do not support that, though it should be
possible with minor adjustments. 