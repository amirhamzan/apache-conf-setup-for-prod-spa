# Apache Configuration Setup for Production SPA

### I've updated the guideline to clarify that it is used for testing a production-like environment locally on Windows 11

This guide explains how to **build**, **deploy**, and **configure Apache** for an Angular project in a production environment.  
Follow these steps to ensure your Angular app runs smoothly with proper routing on an Apache server.

---

## 1. Build Production Files  
Generate the production-ready files by running:

```sh
npm run build-prod
```

Alternatively, if using the Angular CLI:

```sh
ng build --prod
```

This will create a `dist/` folder containing optimized production files.

---

## 2. Deploy to Server/Production Environment  

### **For Local Deployment (Laragon on Windows)**  
Move the **dist/** folder to the web root directory:  
```sh
C:\laragon\www\your-angular-app
```
Ensure the **Apache DocumentRoot** points to this directory.

### **For Remote Server Deployment**  
Transfer the `dist/` folder to the server’s web directory, typically:  
```sh
scp -r dist/ user@your-server:/var/www/html/your-angular-app
```

---

## 3. Configure Apache for Angular Routing  

### **Edit Apache VirtualHost Configuration**  
Modify the Apache configuration file (`httpd-vhosts.conf` or `httpd.conf`) to include:

```apache
<VirtualHost *:80>
    DocumentRoot "C:/laragon/www/your-angular-app"
    ServerName your-angular-app.test
    ServerAlias *.your-angular-app.test

    <Directory "C:/laragon/www/your-angular-app">
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted

        # Enable mod_rewrite for Angular routing
        RewriteEngine On

        # Redirect Angular routes to index.html
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

### **Enable `mod_rewrite` (If Not Already Enabled)**  
- **For Laragon (Windows)**:  
  - Open Laragon → Go to **Apache Modules** → Enable **`mod_rewrite`**  
- **For Manual Setup (httpd.conf)**:  
  Ensure the following line is **uncommented** in `httpd.conf`:  
  ```apache
  LoadModule rewrite_module modules/mod_rewrite.so
  ```

---

## 4. Restart Apache to Apply Changes  
After updating the configuration, restart Apache:  

### **For Laragon (Windows)**  
- Open Laragon  
- **Menu → Apache → Restart**

### **For Linux/macOS Servers**  
Run the following command:  
```sh
sudo systemctl restart apache2   # For Ubuntu/Debian
sudo systemctl restart httpd      # For CentOS/RHEL
```

---

## 5. Verify the Deployment  
Once deployed, open your browser and visit:  
```
http://your-angular-app.test
```
Check if :-
- The homepage loads correctly.
- Internal routes (`/dashboard`, `/property/9888`) **work without 404 errors**.  

### **Troubleshooting**
- If navigation fails, **clear the browser cache** (`Ctrl + Shift + R`).
- Check **Apache error logs** for issues:  
  ```sh
  tail -f /var/log/apache2/error.log   # Ubuntu/Debian
  tail -f /var/log/httpd/error.log     # CentOS/RHEL
  ```

---

## Done!  
Your Angular production build is now set up with Apache!
Enjoy your fully optimized and deployed application. 

