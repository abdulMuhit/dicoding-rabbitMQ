Keterangan dan langkah-langkah:

Untuk build dan push ke repo package github, telah disediakan sh di masing-masing
folder service, dockerfile juga terdapat di masing-masing folder (folder order-service dan folder shipping-service).

file sh :
1. build_push_image_shipping_service.sh
2. build_push_image_order_service.sh

repo package terdapat di file link.txt.

Flow menjalankannya:

A. Menggunakan docker compose:

1. pastikan memiliki docker dan docker compose
2. Jalankan: docker compose up -d
3. tunggu beberapa saat sampai service rabbitMQ sempurna.

B. Menggunakan kubernetes

Persyaratan awal:
1. install minikube
2. install kubectl
3. install istioctl

setelah istioctl ada, setup dengan menjalankan perintah:
istioctl install --set profile=default
kubectl label namespace abdul-dicoding istio-injection=enabled

Jalankan service rabitmq terlebih dahulu, kemudian shipping service dan order service, lalu
setup istioctl

1. kubectl apply -f kubernetes/rabitmq
2. kubectl apply -f kubernetes/shipping-service
3. kubectl apply -f kubernetes/order-service
4. kubectl apply -f kubernetes/gateway

pastikan jumlah pods masing-masing minimal 2, karena menggunakan istio maka akan ada service proxy yang menjadi pendamping service-service kita.

Jalankan port-forward untuk memastikan istio dan service-service terhubung:

kubectl port-forward svc/istio-ingressgateway -n istio-system 80:80

lakukan call api (bisa via postman / plugin Thunder client di vscode) ke endpoint order-service:

method: POST
http://localhost:80/order

payload body:
{
    "order": {
        "book_name": "Harry Potter",
        "author": "J.K Rowling",
        "buyer": "Fikri Helmi Setiawan",
        "shipping_address": "Jl. Batik Kumeli no 50 Bandung"
    }
}

hasilnya bisa di lihat di log:
1. kubectl logs svc/shipping-service -n abdul-dicoding > log-shipping-service.txt
2. kubectl logs svc/order-service -n abdul-dicoding > log-order-service.txt


Terimakasih.


