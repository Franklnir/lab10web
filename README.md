# lab10web
### langkah 1
            <?php
            /**
            * Program sederhana pendefinisian class dan pemanggilan class.
            **/
            class Mobil
            {
            private $warna;
            private $merk;
            private $harga;
            public function __construct()
            {
            $this->warna = "Biru";
            $this->merk = "BMW";
            $this->harga = "10000000";
            }
            public function gantiWarna ($warnaBaru)
            {
            $this->warna = $warnaBaru;
            }
            public function tampilWarna ()
            {
            echo "Warna mobilnya : " . $this->warna;
            }
            }
            // membuat objek mobil
            $a = new Mobil();
            $b = new Mobil();
            // memanggil objek
            echo "<b>Mobil pertama</b><br>";
            $a->tampilWarna();
            echo "<br>Mobil pertama ganti warna<br>";
            $a->gantiWarna("Merah");
            
            $a->tampilWarna();
            // memanggil objek
            echo "<br><b>Mobil kedua</b><br>";
            $b->gantiWarna("Hijau");
            $b->tampilWarna();
            ?>
   ![image](https://github.com/user-attachments/assets/9f2debc7-244b-4e52-acff-37e755444141)

###   langkah 2 membuat class library untuk membuat form dan file form.php
          <?php
          /**
          * Nama Class: Form
          * Deskripsi: CLass untuk membuat form inputan text sederhan
          **/
          class Form
          {
          private $fields = array();
          private $action;
          private $submit = "Submit Form";
          private $jumField = 0;
          public function __construct($action, $submit)
          {
          $this->action = $action;
          $this->submit = $submit;
          }
          public function displayForm()
          {
          echo "<form action='".$this->action."' method='POST'>";
          echo '<table width="100%" border="0">';
          for ($j=0; $j<count($this->fields); $j++) {
          echo "<tr><td
          align='right'>".$this->fields[$j]['label']."</td>";
          echo "<td><input type='text'
          name='".$this->fields[$j]['name']."'></td></tr>";
          }
          echo "<tr><td colspan='2'>";
          echo "<input type='submit' value='".$this->submit."'></td></tr>";
          echo "</table>";
          }
          public function addField($name, $label)
          {
          $this->fields [$this->jumField]['name'] = $name;
          $this->fields [$this->jumField]['label'] = $label;
          $this->jumField ++;
          }
          }
          ?>


### langkah 3 implementasi pemanggilan class library form dan buat file form_input.php
        <?php
        // Menghubungkan dengan file database.php
        include "database.php";
        
        if ($_SERVER['REQUEST_METHOD'] === 'POST') {
            $nim = $_POST['txtnim'];
            $nama = $_POST['txtnama'];
            $alamat = $_POST['txtalamat'];
            $db = new Database();
            $data = [
                'nim' => $nim,
                'nama' => $nama,
                'alamat' => $alamat
            ];
        
            if ($db->insert('mahasiswa', $data)) {
                echo "Data berhasil disimpan!";
            } else {
                echo "Gagal menyimpan data.";
            }
        } else {
            echo "<html><head><title>Mahasiswa</title></head><body>";
        
            class Form
            {
                private $fields = [];
                private $action;
                private $submit = "Submit Form";
                private $jumField = 0;
        
                public function __construct($action, $submit)
                {
                    $this->action = $action;
                    $this->submit = $submit;
                }
        
                public function displayForm()
                {
                    echo "<form action='" . $this->action . "' method='POST'>";
                    echo '<table width="100%" border="0">';
                    for ($j = 0; $j < count($this->fields); $j++) {
                        echo "<tr><td align='right'>" . $this->fields[$j]['label'] . "</td>";
                        echo "<td><input type='text' name='" . $this->fields[$j]['name'] . "'></td></tr>";
                    }
                    echo "<tr><td colspan='2'>";
                    echo "<input type='submit' value='" . $this->submit . "'></td></tr>";
                    echo "</table>";
                    echo "</form>";
                }
                public function addField($name, $label)
                {
                    $this->fields[$this->jumField]['name'] = $name;
                    $this->fields[$this->jumField]['label'] = $label;
                    $this->jumField++;
                }
            }
            $form = new Form("", "Submit Data");
            $form->addField("txtnim", "Nim");
            $form->addField("txtnama", "Nama");
            $form->addField("txtalamat", "Alamat");
        
            echo "<h3>Silahkan isi form berikut ini :</h3>";
            $form->displayForm();
            echo "</body></html>";
        }
        ?>
![image](https://github.com/user-attachments/assets/a85d1a00-b1ee-4f63-9dde-16aa6621bdd3)


### langkah 3 conection query dan membuat file database.php lalu membuat file config.php untuk menyambungkan ke database mysql
          <?php
          class Database {
          protected $host;
          protected $user;
          protected $password;
          protected $db_name;
          protected $conn;
          public function __construct() {
          $this->getConfig();
          $this->conn = new mysqli($this->host, $this->user, $this->password,
          $this->db_name);
          if ($this->conn->connect_error) {
          die("Connection failed: " . $this->conn->connect_error);
          }
          }
          private function getConfig() {
          include_once("config.php");
          $this->host = $config['host'];
          $this->user = $config['username'];
          $this->password = $config['password'];
          
          $this->db_name = $config['db_name'];
          }
          public function query($sql) {
          return $this->conn->query($sql);
          }
          public function get($table, $where=null) {
          if ($where) {
          $where = " WHERE ".$where;
          }
          $sql = "SELECT * FROM ".$table.$where;
          $sql = $this->conn->query($sql);
          $sql = $sql->fetch_assoc();
          return $sql;
          }
          public function insert($table, $data) {
          if (is_array($data)) {
          foreach($data as $key => $val) {
          $column[] = $key;
          $value[] = "'{$val}'";
          }
          $columns = implode(",", $column);
          $values = implode(",", $value);
          }
          $sql = "INSERT INTO ".$table." (".$columns.") VALUES (".$values.")";
          $sql = $this->conn->query($sql);
          if ($sql == true) {
          return $sql;
          } else {
          return false;
          }
          }
          public function update($table, $data, $where) {
          $update_value = "";
          if (is_array($data)) {
          foreach($data as $key => $val) {
          $update_value[] = "$key='{$val}'";
          }
          $update_value = implode(",", $update_value);
          }
          $sql = "UPDATE ".$table." SET ".$update_value." WHERE ".$where;
          $sql = $this->conn->query($sql);
          if ($sql == true) {
          return true;
          } else {
          
          return false;
          }
          }
          public function delete($table, $filter) {
          $sql = "DELETE FROM ".$table." ".$filter;
          $sql = $this->conn->query($sql);
          if ($sql == true) {
          return true;
          } else {
          return false;
          }
          }
          }
          ?>

# pastikan nama database nya sudah sama 
![image](https://github.com/user-attachments/assets/93090e63-6035-4551-9889-65b28bcc3c8f)
![image](https://github.com/user-attachments/assets/11249cff-53d2-42ee-9a0f-c80ca45a15af)
# selanjutnya membuat create table 
![image](https://github.com/user-attachments/assets/353b40fa-304f-4492-8380-da7f623e9ef0)
# lalu input 
![image](https://github.com/user-attachments/assets/7214c60b-dfb2-44da-9d57-f31af8ce5655)
![image](https://github.com/user-attachments/assets/c1f48eec-174b-4464-92d7-4e73c2e5ccbf)
# tampilkan data yang di input dengan select*from
![image](https://github.com/user-attachments/assets/a48acf9a-27b3-4b5f-abf3-8fdbff67ef3a)






### lalu langkah membuat file config.php
        <?php
        $config = [
            'host' => 'localhost',
            'username' => 'root',
            'password' => '',
            'db_name' => 'latihan'
        ];
        ?>


        






            
            
