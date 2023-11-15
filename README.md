# Microservices
Repository ini digunakan untuk kebutuhan kelas Belajar Membangun Arsitektur Microservices


# Docker
Docker adalah platform perangkat lunak yang memungkinkan Anda mengemas, mengirim, dan menjalankan aplikasi di dalam kontainer. Perlu membuat Dockerfile (cek file di repo).


## Docker Images
Docker image adalah paket yang berisi aplikasi dan semua dependensinya, bersama dengan informasi tentang cara menjalankan aplikasi tersebut. Image bersifat statis dan berfungsi sebagai "blueprint" untuk membuat container. Image dapat berisi sistem operasi, runtime, aplikasi, dan pustaka lain yang dibutuhkan. Image bersifat read-only dan tidak dapat diubah setelah dibuat.


- Build sebuah Docker image menggunakan Dockerfile :

``` bash
docker build -t todo-app:v1 .
```


- Cara lihat Daftar Image Docker : 

``` bash
docker images
```


- Cara Hapus Image Menggunakan ID atau Nama : 

``` bash
docker rmi <image_id_or_name>
```

atau

``` bash
docker image rm <image_id_or_name>
```


- Cara Hapus Semua Image yang Tidak Digunakan : 

``` bash
docker image prune -a
```


## Docker Container
Docker container adalah instansi berjalan dari Docker image. Container menyediakan lingkungan yang terisolasi di mana aplikasi dapat dijalankan. Setiap container dibuat dari Docker image dan memiliki sistem file yang terpisah, proses, dan ruang nama jaringan yang terisolasi. Container bersifat ringan, cepat untuk dibuat, dan dapat dijalankan di berbagai lingkungan.


- Jalankan container dari image dengan nama container todo-app dan nama image beserta versinya, jika tidak menulis versi maka sama halnya dengan latest :

``` bash
docker run -dp 3000:3000 --name todo-app todo-app:v1
```


- Cara stop container todo-app : 

``` bash
docker stop todo-app
```

- Cara start container todo-app : 

``` bash
docker start todo-app
```


- Cara restart container todo-app : 

``` bash
docker restart todo-app
```


- Cara menghapus container todo-app : 

``` bash
docker rm -f todo-app
```


## Docker Volume
Docker volume adalah mekanisme yang memungkinkan data persisten disimpan di luar container. Volume menyediakan cara untuk menyimpan dan berbagi data antara container dan sistem host. Dengan menggunakan volume, data dapat tetap ada bahkan setelah container dihapus. Volume berguna untuk menyimpan database, file konfigurasi, dan data persisten lainnya. Docker volume dapat di-attach ke dalam container saat menjalankannya.


- Buat Volume dengan nama todo-db :

``` bash
docker volume create todo-db
```


- Jalankan Container menggunakan Volume todo-db (Volume tersebut akan dihasilkan di dalam direktori /var/lib/docker/volumes/todo-db/) :

``` bash
docker run -dp 3000:3000 --name todo-app -v todo-db:/etc/todos todo-app:v1
```


- Untuk mengakses data secara langsung :

``` bash
docker volume inspect todo-db
```


- Untuk Lihat Daftar Volume yang Ada :

``` bash
docker volume ls
```


- Hapus Volume Menggunakan Nama :

``` bash
docker volume rm <volume_name>
```


- Hapus Semua Volume yang Tidak Digunakan :

``` bash
docker volume prune
```


## Docker Network
Docker network adalah jaringan virtual yang memungkinkan container berkomunikasi satu sama lain, baik di dalam satu host atau di seluruh jaringan yang terhubung. Dengan menggunakan Docker network, Anda dapat membuat jaringan yang terisolasi untuk menghubungkan container, memberikan nama kepada container untuk mengidentifikasinya di dalam jaringan, dan menetapkan port untuk menghubungkan container dengan dunia luar.


- Secara default, saat instalasi Docker, kita akan mendapatkan tiga driver network, yakni Bridge, Host, dan None. Buat melihat list network :

``` bash
docker network ls
```


- Melihat informasi rinci tentang bridge network yang dibuat oleh Docker pada host docker network inspect bridge :

``` bash
docker network inspect bridge
```


- Cara membuat Network dengan nama my-bridge-network :

``` bash
docker network create my-bridge-network
```


- Cara buat Overlay Network (untuk Swarm) :

``` bash
docker network create --driver overlay my-overlay-network
```


- Cara buat Custom Bridge Network :

``` bash
docker network create --driver bridge --subnet 192.168.0.0/24 --gateway 192.168.0.1 my-custom-bridge
```


- Cara Menghapus Docker Network :

``` bash
docker network rm my-bridge-network
```


## Log
Untuk menampilkan log keluaran (output log) dari container Docker :

``` bash
docker logs todo-app
```



# Example

## MySQL menggunakan Docker

``` bash
docker run -d \
     --name mysql-db \
     --network todo-app --network-alias mysql \
     -v todo-mysql-data:/var/lib/mysql \
     -e MYSQL_ROOT_PASSWORD=passwordnya \
     -e MYSQL_DATABASE=todo-db \
     mysql:5.7
```


## NodeJS menggunakan Docker

``` bash
docker run -dp 3000:3000 --name todo-app \
   -w /app -v "$(pwd):/app" \
   --network todo-app \
   -e MYSQL_HOST=mysql \
   -e MYSQL_USER=root \
   -e MYSQL_PASSWORD=passwordnya \
   -e MYSQL_DB=todo-db \
   node:12-alpine \
   sh -c "yarn install && yarn run dev"
```


## Masuk ke MySQL di Docker

``` bash
docker exec -it mysql-db mysql -u root -p
```


# Docker Composer
Docker Compose adalah alat yang memungkinkan Anda mendefinisikan dan menjalankan aplikasi Docker multi-container dengan menggunakan berkas YAML untuk mendefinisikan layanan, jaringan, dan volume. Dengan Docker Compose, Anda dapat dengan mudah menyusun dan mengelola aplikasi yang terdiri dari beberapa container, mempermudah proses pengembangan, uji, dan implementasi. Untuk itu perlu dibuatkan file bernama docker-compose.yml (cek file di repo).

``` yaml
version: "3"
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
  database:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: example
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
```


## Docker Compose Up
Membuat dan menjalankan kontainer berdasarkan definisi dalam berkas docker-compose.yml dan opsi -d atau --detach digunakan untuk menjalankan kontainer dalam mode latar belakang (detached mode).

``` bash
docker-compose up -d
```


## Docker Compose Down
Menghentikan dan menghapus semua kontainer, jaringan, dan volume yang didefinisikan dalam berkas docker-compose.yml.

``` bash
docker-compose down
```


## Menampilkan Status
Menampilkan status kontainer yang didefinisikan dalam berkas docker-compose.yml.

``` bash
docker-compose ps
```


## Menampilkan Log
Menampilkan log dari kontainer yang didefinisikan dalam berkas docker-compose.yml.

``` bash
docker-compose logs
```


## Mengakses Docker Compose
Menjalankan perintah di dalam kontainer yang didefinisikan dalam berkas docker-compose.yml.

``` bash
docker-compose exec web ls /usr/share/nginx/html
```


## Penggunaan Volume
Dalam contoh ini, direktori lokal ./html akan di-mount ke dalam direktori /usr/share/nginx/html di dalam kontainer web.

``` yaml
volumes:
  - ./html:/usr/share/nginx/html
```


## Penggunaan Network
Secara default, Docker Compose akan membuat network bersama untuk semua kontainer. Jika Anda ingin menetapkan nama jaringan atau mengonfigurasi opsi jaringan lebih lanjut, Anda dapat melakukannya dalam berkas docker-compose.yml.

``` yaml
networks:
  my_network:
    driver: bridge
```

Setelah menetapkan network seperti di atas, Anda dapat menggunakan opsi networks pada level layanan untuk menyertakan kontainer ke dalam network yang diberi nama tersebut. Misalnya:

``` yaml
services:
  web:
    networks:
      - my_network
```