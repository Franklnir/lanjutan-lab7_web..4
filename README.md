# lanjutan-lab7_web..4

# PRAKTIKUM 4
### langkah 1.buat table user
![image](https://github.com/user-attachments/assets/ceffb2a4-e1e9-4e23-bd95-b9cf0d6a62af)

### langkah 2.Buat file baru pada direktori app/Models dengan nama UserModel.php
                <?php
                namespace App\Models;
                use CodeIgniter\Model;
                class UserModel extends Model
                {
                protected $table = 'user';
                protected $primaryKey = 'id';
                protected $useAutoIncrement = true;
                protected $allowedFields = ['username', 'useremail', 'userpassword'];
                }

### langkah 2.buat nama User.php pada direktori app/Controllers.
                                              <?php
                                              namespace App\Controllers;
                                              use App\Models\UserModel;
                                              class User extends BaseController
                                              {
                                              public function index()
                                              {
                                              $title = 'Daftar User';
                                              $model = new UserModel();
                                              $users = $model->findAll();
                                              return view('user/index', compact('users', 'title'));
                                              }
                                              public function login()
                                              {
                                              helper(['form']);
                                              $email = $this->request->getPost('email');
                                              $password = $this->request->getPost('password');
                                              if (!$email)
                                              {
                                              return view('user/login');
                                              }
                                              $session = session();
                                              $model = new UserModel();
                                              $login = $model->where('useremail', $email)->first();
                                              if ($login)
                                              {
                                              $pass = $login['userpassword'];
                                              if (password_verify($password, $pass))
                                              {
                                              $login_data = [
                                              'user_id' => $login['id'],
                                              'user_name' => $login['username'],
                                              'user_email' => $login['useremail'],
                                              'logged_in' => TRUE,
                                              ];
                                              $session->set($login_data);
                                              return redirect('admin/artikel');
                                              }
                                              else
                                              {
                                              $session->setFlashdata("flash_msg", "Password salah.");
                                              return redirect()->to('/user/login');
                                              }
                                              }
                                              else
                                              {
                                              $session->setFlashdata("flash_msg", "email tidak terdaftar.");
                                              return redirect()->to('/user/login');
                                              }
                                              }
                                              }

###  langkah 3.Buat direktori baru dengan nama user pada direktori app/views, kemudian buat file baru dengan nama login.php.
### Membuat Database Seeder 
          php spark make:seeder UserSeeder


### langkah 4.buka file UserSeeder.php yang berada di lokasi direktori /app/Database/Seeds/UserSeeder.php
![image](https://github.com/user-attachments/assets/0bce01dd-d237-4da1-86c9-dcfa13d8fe29)
![image](https://github.com/user-attachments/assets/3b6ba582-16c1-4121-b3f7-6e7da89dc9ce)

### langkah 5.php spark db:seed UserSeeder

![image](https://github.com/user-attachments/assets/8fb64c3c-e194-45ea-9efb-f633f2bc9577)

### langkah 6.Auth.php pada direktori app/Filters.
                          <?php
                          
                          namespace App\Filters;
                          
                          use CodeIgniter\HTTP\RequestInterface;
                          use CodeIgniter\HTTP\ResponseInterface;
                          use CodeIgniter\Filters\FilterInterface;
                          
                          class Auth implements FilterInterface
                          {
                              public function before(RequestInterface $request, $arguments = null)
                              {
                                  // Jika user belum login, redirect ke halaman login
                                  if (! session()->get('logged_in')) {
                                      return redirect()->to('/user/login');
                                  }
                              }
                          
                              public function after(RequestInterface $request, ResponseInterface $response, $arguments = null)
                              {
                                  // Tidak melakukan apa-apa setelah response
                              }
                          }


### langkah 7.buka file app/Config/Filters.php tambahkan kode berikut:
        'auth' => App\Filters\Auth::class
### langkah 8.buka file app/Config/Routes.php dan sesuaikan kodenya.
![image](https://github.com/user-attachments/assets/75601b1d-9bfa-4034-91aa-a6c17a5c7692)
![image](https://github.com/user-attachments/assets/6b842180-fccc-4d9f-ae6c-97f681c607d8)
![image](https://github.com/user-attachments/assets/332bae95-8f54-467b-8bb5-7b6776e5cfc0)

### langkah 8.Tugas Lanjutan (Improvisasi)
                    public function logout()
                    {
                        session()->destroy();
                        return redirect()->to('/user/login')->with('message', 'Berhasil logout.');
                    }

### langkah 9.di halaman login (Views/user/login.php)
                            <?php if (session()->getFlashdata('message')): ?>
                                <div class="alert alert-success">
                                    <?= session()->getFlashdata('message') ?>
                                </div>
                            <?php endif; ?>

menambah tampilan login 
![image](https://github.com/user-attachments/assets/10b6b358-ba7b-4d63-a256-3d73bc1d5466)


![image](https://github.com/user-attachments/assets/7d2e71b2-10f7-42b6-ac3f-a1f5042f17a4)



# PRAKTIKUM 5

### langkah 1.kemudian modifikasi kode pada method admin_index
### langkah 2.tambahkan file views/artikel/admin_index.php
          <?= $pager->links(); ?>
![image](https://github.com/user-attachments/assets/209c391b-9521-4bc0-ae57-e7673aa780ce)

### langkah 3.Membuat Pencarian
![image](https://github.com/user-attachments/assets/7b7fc150-8d51-4da9-90cc-cbfbdd74303d)
![image](https://github.com/user-attachments/assets/75e9f114-3cf1-43a4-a813-8655ade6638d)

### langkah 4.buka kembali Controller Artikel, pada method admin_index ubah kodenya seperti berikut
                      public function admin_index()
                      {
                      $title = 'Daftar Artikel';
                      $q = $this->request->getVar('q') ?? '';
                      $model = new ArtikelModel();
                      $data = [
                      'title' => $title,
                      'q' => $q,
                      'artikel' => $model->like('judul', $q)->paginate(10), # data
                      dibatasi 10 record per halaman
                      'pager' => $model->pager,
                      ];
                      return view('artikel/admin_index', $data);
                      }
                      <form method="get" class="form-search">
                      <input type="text" name="q" value="<?= $q; ?>" placeholder="Cari data">
                      <input type="submit" value="Cari" class="btn btn-primary">
                      </form>

### langkah 5.buka kembali file views/artikel/admin_index.php
            <form method="get" class="form-search">
            <input type="text" name="q" value="<?= $q; ?>" placeholder="Cari data">
            <input type="submit" value="Cari" class="btn btn-primary">
            </form>

### langkah 6.Dan pada link pager ubah seperti berikut.
          <?= $pager->only(['q'])->links(); ?>

![image](https://github.com/user-attachments/assets/ee286eb8-c4b2-4f86-8646-28113f3386e0)
![image](https://github.com/user-attachments/assets/59d009ef-df83-4233-ac48-7af89fe7c0b8)


# PRAKTIKUM 6
### langkah 1.edit kembali Controller Artikel pada project sebelumnya, sesuaikan kode pada method add seperti berikut:
                                  public function add()
                                  {
                                  // validasi data.
                                  $validation = \Config\Services::validation();
                                  $validation->setRules(['judul' => 'required']);
                                  $isDataValid = $validation->withRequest($this->request)->run();
                                  if ($isDataValid)
                                  {
                                  $file = $this->request->getFile('gambar');
                                  $file->move(ROOTPATH . 'public/gambar');
                                  $artikel = new ArtikelModel();
                                  $artikel->insert([
                                  'judul' => $this->request->getPost('judul'),
                                  'isi' => $this->request->getPost('isi'),
                                  'slug' => url_title($this->request->getPost('judul')),
                                  'gambar' => $file->getName(),
                                  ]);
                                  return redirect('admin/artikel');
                                  }
                                  $title = "Tambah Artikel";
                                  return view('artikel/form_add', compact('title'));
                                  }
### langkah 2.pada file views/artikel/form_add.php tambahkan field
                  <p>
                  <input type="file" name="gambar">
                  </p>
### langkah 3.sesuaikan tag form dengan menambahkan ecrypt type seperti berikut.
                        <form action="" method="post" enctype="multipart/form-data">
![image](https://github.com/user-attachments/assets/a224d516-4ed9-4841-98ce-ed79e8bbd671)







                                                                                            
                                              
