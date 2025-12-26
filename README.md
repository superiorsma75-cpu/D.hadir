# D.hadir
<?php
session_start();
$conn = mysqli_connect("localhost","root","","absensi_sekolah");

$username = $_POST['username'];
$password = md5($_POST['password']);

$query = mysqli_query($conn,
    "SELECT * FROM users 
     WHERE username='$username' 
     AND password='$password'");

$data = mysqli_fetch_assoc($query);

if($data){
    $_SESSION['user_id'] = $data['id'];
    $_SESSION['role'] = $data['role'];
    header("location:dashboard.php");
}else{
    echo "Login gagal";
}
?>
<?php
session_start();
if($_SESSION['role']=='guru'){
    echo "Dashboard Guru";
}else{
    echo "Dashboard Siswa";
}
?>
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <title>Daftar Hadir Siswa</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f4f6f8;
            margin: 0;
            padding: 20px;
        }
        h1 {
            text-align: center;
            color: #2c3e50;
        }
        .container {
            max-width: 800px;
            margin: auto;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        input, select, button {
            width: 100%;
            padding: 10px;
            margin-top: 10px;
        }
        button {
            background: #3498db;
            color: white;
            border: none;
            cursor: pointer;
            border-radius: 5px;
        }
        button:hover {
            background: #2980b9;
        }
        table {
            width: 100%;
            margin-top: 20px;
            border-collapse: collapse;
        }
        table, th, td {
            border: 1px solid #ccc;
        }
        th, td {
            padding: 10px;
            text-align: center;
        }
        th {
            background: #3498db;
            color: white;
        }
        .hapus {
            background: red;
            color: white;
            border: none;
            padding: 5px 10px;
            cursor: pointer;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>Daftar Hadir Siswa</h1>

    <input type="text" id="nama" placeholder="Nama Siswa">
    <input type="text" id="kelas" placeholder="Kelas">
    
    <select id="status">
        <option value="Hadir">Hadir</option>
        <option value="Izin">Izin</option>
        <option value="Sakit">Sakit</option>
        <option value="Alpha">Alpha</option>
    </select>

    <button onclick="tambahAbsen()">Simpan Absensi</button>

    <table>
        <thead>
            <tr>
                <th>No</th>
                <th>Nama</th>
                <th>Kelas</th>
                <th>Status</th>
                <th>Tanggal & Jam</th>
                <th>Aksi</th>
            </tr>
        </thead>
        <tbody id="dataAbsen"></tbody>
    </table>
</div>

<script>
    let absensi = JSON.parse(localStorage.getItem("absensi")) || [];

    function tampilkan() {
        let tabel = document.getElementById("dataAbsen");
        tabel.innerHTML = "";
        absensi.forEach((data, index) => {
            tabel.innerHTML += `
                <tr>
                    <td>${index + 1}</td>
                    <td>${data.nama}</td>
                    <td>${data.kelas}</td>
                    <td>${data.status}</td>
                    <td>${data.waktu}</td>
                    <td><button class="hapus" onclick="hapus(${index})">Hapus</button></td>
                </tr>
            `;
        });
    }

    function tambahAbsen() {
        let nama = document.getElementById("nama").value;
        let kelas = document.getElementById("kelas").value;
        let status = document.getElementById("status").value;

        if (nama === "" || kelas === "") {
            alert("Nama dan kelas wajib diisi!");
            return;
        }

        let waktu = new Date().toLocaleString("id-ID");

        absensi.push({ nama, kelas, status, waktu });
        localStorage.setItem("absensi", JSON.stringify(absensi));

        document.getElementById("nama").value = "";
        document.getElementById("kelas").value = "";

        tampilkan();
    }

    function hapus(index) {
        absensi.splice(index, 1);
        localStorage.setItem("absensi", JSON.stringify(absensi));
        tampilkan();
    }

    tampilkan();
</script>

</body>
</html>
