
#PA-PC_202131112_RUTH-SYALOMITHA-HUTABARAT_PENGOLAHAN-CITRA-F

# Deteksi Plat Nomor
Merupakan proses mengidentifikasi dan membedakan plat nomor kendaraan dari gambar menggunakan teknik pemrosesan gambar. Tujuannya adalah untuk mendapatkan citra plat nomor yang terisolasi sehingga dapat dilakukan analisis lebih lanjut, seperti pengenalan  atau verifikasi pelat nomor.

# Langkah-Langkah
- Buat folder, Lalu Buka jupyter 
- Kemudian Create new File Python Jupyter notebook dan diletakkan pada folder yang telah dibuat tadi.
- Jangan lupa untuk menginstal library OpenCV terlebih dahulu
- Lalu import fungsi-fungsi yang akan digunakan, seperti berikut:


```bash
import cv2
import numpy as np
import matplotlib.pyplot as plt
```

##
- Read Image
```bash
image = cv2.imread(image_path)
```
- Apply Gaussian blur to reduce noise
```bash
blurred = cv2.GaussianBlur(gray, (5, 5), 0)
```
- Deteksi tepi menggunakan Canny
```bash
edges = cv2.Canny(blurred, 50, 150)
```
- Dilation and Erosion untuk menutup celah di antara tepi objek
```bash
kernel = np.ones((3, 3), np.uint8)
closed = cv2.morphologyEx(edges, cv2.MORPH_CLOSE, kernel)
```
- contours
```bash
 contours, _ = cv2.findContours(closed.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
 contours = sorted(contours, key=cv2.contourArea, reverse=True)
 for contour in contours:
        epsilon = 0.02 * cv2.arcLength(contour, True)
        approx = cv2.approxPolyDP(contour, epsilon, True)
        if len(approx) == 4:
``` 
- membuat kotak di daerah contour
    ```bash    
            x, y, w, h = cv2.boundingRect(approx)
            cv2.rectangle(image, (x, y), (x + w, y + h), (0, 255, 0), 3)

            # Crop the license plate region
            license_plate = image[y:y + h, x:x + w]
    ``` 
- Convert the license plate region to grayscale

```bash 
            plate_gray = cv2.cvtColor(license_plate, cv2.COLOR_BGR2GRAY)
```
- Perform thresholding to create a binary image
```bash
         _, plate_binary = cv2.threshold(plate_gray, 0, 255, cv2.THRESH_BINARY | cv2.THRESH_OTSU)
```
- Gambar Asli
```bash
            plt.figure(figsize=(12, 8))
            plt.subplot(2, 2, 1)
            plt.imshow(cv2.cvtColor(image, cv2.COLOR_BGR2RGB))
            plt.title("Original Image")
            plt.axis("off")
```
- Plat terdeteksi :
```bash
            plt.subplot(2, 2, 2)
            plt.imshow(cv2.cvtColor(license_plate, cv2.COLOR_BGR2RGB))
            plt.title("Detected License Plate")
            plt.axis("off")
```
- Binary Image :
```bash
            plt.subplot(2, 2, 3)
            plt.imshow(plate_binary, cmap="gray")
            plt.title("Binary Image")
            plt.axis("off")
```
- Edge Image :
```bash
            plt.subplot(2, 2, 4)
            plt.imshow(edges, cmap="gray")
            plt.title("Edges Image")
            plt.axis("off")

            plt.tight_layout()
            plt.show()

            break
```
- Gambar yang digunakan:
```bash
image_path = "plat.jpg"
detect_license_plate(image_path)
```