
masukan dan buat ssh-keygen di clients
ls -l ~/.ssh # untuk melihat isi si file ssh nya
menampilkan isi file nya menggunakn cat ~/.ssh/id....pub

copy public key ke server kita
ssh ke server yang kita punya dan masukan secara manual
ssh-copy-id raka@192.168.1.12
masukan pass kita nya

dan jika kita masukan lagi sshnya maka akan langsung masukan
jika ketikan ls -l ~/ssh maka akan tertampilkan file authorized_keys
jika di cat maka akan berisi pub yang sama

