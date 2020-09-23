# Kubernetes
Kubernetes merupakan platform open-source yang digunakan untuk melakukan manajemen workloads aplikasi yang dikontainerisasi, serta menyediakan konfigurasi dan otomatisasi secara deklaratif.

## Ekspose Service Nodeport
### template nodeport
kita membuat namafile.yaml pada spec type kita tuliskan NodePort karena kita akan mengekspose service dari nodeport begitupun padda ports kita tambahkan nodeport:(port).

```
apiVersion: v1
kind: Service
metadata:
  name: service-name
  labels:
    label-key1: label-value1
spec:
  type: NodePort
  selector:
    label-key1: label-value1
  ports:
    - port: 80
      targetPort: 80
      nodePort: 32767
```
### create service
untuk menjalankan perintah membuat pod jalankan perintah berikut:
```
kubectl create -f namafile.yaml
```
tunggu sampai semua service selesai

### ekspose nodeport di minikube
```
minikube servise nama-servise
```
## Ekspose Service Load Balancer
### template  load balancer
- Cloud Provider seperti Google Cloud atau Amazon Web Service biasanya memiliki Cloud LoadBalancer
- Kubernetes bisa menggunakan LoadBalancer bawaan dari Cloud Provider sebagai cara untuk mengekspos Service
- LoadBalancer akan melakukan load balance request ke NodePort
- Sayangnya Service LoadBalancer ini tidak bisa di test di local seperti menggunakan Minikube

sama seperti nodeport pada load balancer hanya mengganti nama type nya menjadi LoadBalancer dan tidak ada port tambahan.
```
apiVersion: v1
kind: Service
metadata:
  name: service-name
  labels:
    label-key1: label-value1
spec:
  type: LoadBalancer
  selector:
    label-key1: label-value1
  ports:
    - port: 80
      targetPort: 80
```
## Ingress
### apa itu Ingress
- Ingress adalah salah satu cara yang bisa digunakan untuk mengekspos Service.
- Berbeda dengan LoadBalancer atau NodePort, jika menggunakan Ingress, client hanya butuh tahu satu lokasi ip adddress Ingress
- Ketika client melakukan request ke Ingress, pemilihan service nya ditentukan menggunakan hostname dari request
- Ingress hanya mendukung protocol HTTP
### menambahkan addons Ingress

melihat addons yang sudah terinstall
```
minikube addons lits
```
menginstall Ingress
```
minikube addons enable Ingress
```
melihat Ingress
```
kubectl get pods --namespace kube-system
```

### membuat Ingress
buat file dengan nama Ingress.yaml

template
```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: ingress-name
  labels:
    label-key1: label-value1
spec:
  rules:
    - host: sub.domain.com
      http:
        paths:
          - path: /
            backend:
              serviceName: service-name
              servicePort: 80
```
### Melihat Ingress
```
kubectl get ingresses
```
### Menghapus Ingress
```
kubectl delete ingress namaingress
```
### Melihat Ip Minikube
```
minikube ip
```
### Setting Host
```
// Edit Sistem Operasi Hosts File
yoursub.domain.com minikubeip
```

refrensi:

[Programmer Zaman Now](https://www.youtube.com/playlist?list=PL-CtdCApEFH8XrWyQAyRd6d_CKwxD8Ime)

[Slide](https://docs.google.com/presentation/d/1NJQqJd89k1od_o9Kz-79IQ_CQYDCJKWunCtpgkHQLi4/edit#slide=id.p)
