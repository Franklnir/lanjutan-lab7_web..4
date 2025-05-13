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
                                                                                            
                                              
