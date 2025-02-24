<template>
  <nav class="navbar navbar-expand-lg bg-light p-2 shadow fixed-top">
    <div class="container-fluid d-flex">
      <nuxt-link to="/dashboard" class="d-flex tohome">
          <img src="../assets/img/logo.png" alt="logo" class="logo mt-2">
          <div class="text mt-2">
            <h4 class="fw-medium">CABAPUS</h4>
            <p class="m-0">Catatan Barang Perpustakaan Universitas Siliwangi</p>
          </div>
      </nuxt-link>
      <div class="nav">
        <ul class="ms-auto">
          <li class="nav-item dropdown d-flex">
            <i class="bi bi-person-fill-check fs-2"></i>              
            <h5 class="dropdown-toggle mt-3" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                  Admin
            </h5>
            <ul class="dropdown-menu">
                <li><nuxt-link to="/logout" class="dropdown-item">Logout</nuxt-link></li> 
            </ul>
          </li>
        </ul>
      </div>
    </div>
  </nav>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,100..700;1,100..700&display=swap');

h4, h5, p, .btn, .nav-link {
    font-family: "Josefin Sans", serif;
}
.logo {
  width: 4rem;
  height: 4rem;
}

.tohome {
  color: #000000 !important;
  text-decoration: none;
  margin-left: 1.5rem;
}

.text {
  margin-left: 1rem;
}

.btn {
  width: 9rem;
  font-size: 1.2rem;
  background-color: #BCCCDC;
}

.nav {
  margin-right: 3rem;
}

.nav-link {
  font-size: 1.4rem;
}

h5 {
  margin-left: 0.5rem;
}

@media only screen and (max-width: 600px) {
  .logo {
    width: 2.5rem;
    height: 2.5rem;
    margin-top: 0rem !important;
  }

  h4 {
    font-size: 1rem;
    margin-top: 0.4rem;
  }

  .tohome {
    margin-left: 0;
    top: 0;
  }

  p {
    display: none;
  }

  i {
    font-size: 1.4rem !important;
    margin-top: 0.2rem;
  }

  h5 {
    font-size: 0.7rem;
  }

  .nav {
    margin-right: 0.5rem;
    margin-top: 1rem;
  }
}

@media only screen and (min-width: 600px) and (max-width: 890px) {
  .logo {
    width: 2.5rem;
    height: 2.5rem;
    margin-top: 0rem !important;
  }

  h4 {
    font-size: 1rem;
    margin-top: 0.4rem;
  }

  .tohome {
    margin-left: 1rem !important;
    top: 0;
  }

  p {
    display: none;
  }

  .nav {
    margin-top: 0.5rem;
  }
  
}

</style>
