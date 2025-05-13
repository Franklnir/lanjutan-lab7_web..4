# lanjutan-lab7_web..4

# PRAKTIKUM 3
### buat table user
![image](https://github.com/user-attachments/assets/ceffb2a4-e1e9-4e23-bd95-b9cf0d6a62af)

### Buat file baru pada direktori app/Models dengan nama UserModel.php
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

### buat nama User.php pada direktori app/Controllers.
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

###  Buat direktori baru dengan nama user pada direktori app/views, kemudian buat file baru dengan nama login.php.
### Membuat Database Seeder 
          php spark make:seeder UserSeeder


### buka file UserSeeder.php yang berada di lokasi direktori /app/Database/Seeds/UserSeeder.php
### php spark db:seed UserSeeder

![image](https://github.com/user-attachments/assets/8fb64c3c-e194-45ea-9efb-f633f2bc9577)

### Auth.php pada direktori app/Filters.
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


### buka file app/Config/Filters.php tambahkan kode berikut:
        'auth' => App\Filters\Auth::class
### buka file app/Config/Routes.php dan sesuaikan kodenya.
![image](https://github.com/user-attachments/assets/75601b1d-9bfa-4034-91aa-a6c17a5c7692)
![image](https://github.com/user-attachments/assets/6b842180-fccc-4d9f-ae6c-97f681c607d8)
![image](https://github.com/user-attachments/assets/332bae95-8f54-467b-8bb5-7b6776e5cfc0)

### Tugas Lanjutan (Improvisasi)
                    public function logout()
                    {
                        session()->destroy();
                        return redirect()->to('/user/login')->with('message', 'Berhasil logout.');
                    }

### di halaman login (Views/user/login.php)
                            <?php if (session()->getFlashdata('message')): ?>
                                <div class="alert alert-success">
                                    <?= session()->getFlashdata('message') ?>
                                </div>
                            <?php endif; ?>

![image](https://github.com/user-attachments/assets/7d2e71b2-10f7-42b6-ac3f-a1f5042f17a4)






                                                                                            
                                              
