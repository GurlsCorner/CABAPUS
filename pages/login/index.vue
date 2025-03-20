<script setup>

const supabase = useSupabaseClient()
const email = ref ("")
const password = ref ("")
const errorMessage = ref("")

async function SignIn() {
  const { data, error } = await supabase.auth.signInWithPassword({
    email: email.value,
    password: password.value,
  })
  if (error) {
    errorMessage.value = "Email atau password salah"
  }
  if (data) {
    navigateTo ("/dashboard")
  }
}

definePageMeta ({
    layout: 'login'
})

</script>

<template>
    <div class="container-fluid p-0 mb-0">

      <div class="row">
        <div class="col">
          <div class="card card-back mt-3 rounded-4">
            <nuxt-link to="/" class="no-underline">
            <div class="d-flex m-2">
              <span class="span-first text-center rounded-3"><i class="bi bi-arrow-bar-left fs-4"></i></span>
                <h6 class="mt-2">Kembali</h6>
              </div>
            </nuxt-link>
          </div>
        </div>
      </div>

        <div class="text-center p-5">
            <h2>Silahkan login, <b>Admin</b></h2>
        </div>

        <div class="row justify-content-center m-0">
            <div class="col-8 col-md-7 col-lg-4">
                <div class="card card-login rounded-4 shadow">
                    <div class="card-body m-3">
                        <div class="text-center">
                            <img src="~/assets/img/logo.png" alt="logo" class="logo">
                            <h3 class="mt-4 fw-medium">CABAPUS</h3>
                            <p>Catatan Barang Perpustakaan Universitas Siliwangi</p>
                        </div>
                        <form @submit.prevent="SignIn">
                            <div class="mb-3">
                                <label for="exampleInputEmail1" class="form-label">Email</label>
                                <input v-model="email" type="email" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp">
                            </div>
                            <div class="mb-3">
                                <label for="exampleInputPassword1" class="form-label">Password</label>
                                <input v-model="password" type="password" class="form-control" id="exampleInputPassword1">
                            </div>
                            <p v-if="errorMessage" class="text-center">{{ errorMessage }}</p>
                            <div class="text-center mt-5">
                                <button type="submit" class="btn rounded-5">Login</button>
                            </div>
                        </form> 
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Kavoon&family=Miltonian+Tattoo&family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&family=Red+Rose:wght@300..700&display=swap');


.container-fluid {
    background: url("~/assets/img/background.jpg") no-repeat center center;
    background-size: cover;
    height: 130vh; 
}


h3, h6, .text-center, label, p {
    font-family: "Josefin Sans", serif;
}

h2 {
    background-color: #9AA6B280;
    width: 50%;
    margin: auto;
    color: #fff;

}

.btn {
    width: 15rem;
    color: #fff;
    background-color: #9AA6B2;
}

.card-login {
    background-color: #ffffff99;
    border: 3px solid #ffff;
}

img {
    width: 5rem;
}

.card-back {
  width: 7rem;
  height: 3rem;
  margin-left: 1rem;
  background-color: #BCCCDC;
}

i {
  color: #fff;
}

h6 {
  color: #fff;
}

.no-underline {
  text-decoration: none;
}

@media only screen and (max-width: 600px) {
  .container-fluid {
      background-size: cover;
      height: 105vh; 
  }

  .logo {
    width: 3rem;
    height: 3rem;
    margin-top: 0 !important;
  }

  h2 {
    font-size: 1.2rem;
  }

  h3 {
    font-size: 1rem;
  }

  p {
    font-size: 0.7rem;
  }

  label {
    font-size: 0.8rem;
  }

  .btn {
    padding-top: 0.4rem;
    width: 7rem;
    height: 1.7rem;
    font-size: 0.7rem;
  }
  
  .login {
    margin-top: 2rem;
  }

  .form-control {
    height: 1.5rem;
  }

  .card-back {
    width: 5rem;
    height: 2.3rem;
    margin-left: 0.5rem;
  }

  i {
    margin-top: 0 !important;
    font-size: 1rem !important;
    color: #fff;
  }

  h6 {
    margin-top: 0.2rem !important;
    font-size: 0.8rem !important;
    color: #fff;
  }

  input {
      font-size: 0.7rem;
  }

}

@media only screen and (min-width: 600px) and (max-width: 890px) {
  .container-fluid {
      background-size: cover;
      height: 105vh; 
  }

  input {
      font-size: 0.7rem;
  }
}

</style>