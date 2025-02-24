<script setup>
const route = useRoute()
const router = useRouter()

const hiddenRoutes = ['login']

const shouldShowNavbar = !hiddenRoutes.includes(route.name)

const isActive = ref('home');

const setActive = (tab) => {
  isActive.value = tab;
};

</script>


<template>
  <nav v-if="route?.name != 'login'" class="navbar navbar-expand-lg bg-light p-3 shadow fixed-top">
    <div class="container-fluid d-flex">
      <nuxt-link to="/" class="d-flex tohome">
          <img src="../assets/img/logo.png" alt="logo" class="logo mt-2">
          <div class="text mt-2">
            <h4 class="fw-medium">CABAPUS</h4>
            <p>Catatan Barang Perpustakaan Universitas Siliwangi</p>
          </div>
      </nuxt-link>
      <div class="nav">
          <ul class="navbar-nav ms-auto">
            <li class="nav-item text-center">
              <nuxt-link to="/login" :class="{ active: isActive === 'home' }" @click="setActive('home')">
                <button class="btn">LOGIN</button>
              </nuxt-link>
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

form {
  margin-left: 200p;
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
  height: 2.5rem;
  font-size: 1rem;
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
    margin-top: 0;
  }

  .btn {
    width: 5rem;
    height: 2rem;
    margin-top: 0.2rem;
    font-size: 0.7rem !important;
    padding-top: 0.5rem;
  }
}

</style>



<!-- <template>
    <nav class="navbar navbar-expand-lg bg-light p-2 shadow fixed-top">
      <div class="container-fluid">
        <nuxt-link to="/" class="d-flex tohome">
            <img src="../assets/img/logo.png" alt="logo" class="logo mt-2">
            <div class="text mt-2">
              <h4 class="fw-medium">Pensil</h4>
              <p>Pengambilan Barang Perpustakaan Universitas Siliwangi</p>
            </div>
        </nuxt-link>
        <div class="nav">
          <ul class="navbar-nav ms-auto">
            <li class="nav-item text-center">
              <button class="btn">LOGIN</button>
            </li>
          </ul>
        </div>
      </div>
    </nav>
</template>
  
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,100..700;1,100..700&display=swap');

h4, p, .btn, .nav-link {
    font-family: "Josefin Sans", serif;
}
.logo {
    width: 4rem;
    height: 4rem;
}

form {
    margin-left: 200p;
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
    width: 10rem;
    height: 2.3rem;
    font-size: 1.2rem;
    background-color: #BCCCDC;
}

.navbar-nav {
    margin-right: 3rem;
}

.nav-link {
    font-size: 1.4rem;
}

@media only screen and (max-width: 600px) {
  .logo {
    width: 3rem;
    height: 3rem;
  }

  h4 {
    font-size: 1rem;
  }

  .tohome {
    margin-left: 0;
  }

  .btn {
    font-size: 0.7rem !important;
    margin-top: 0.2rem;
    width: 6rem;
    height: 2rem;
  }

  p {
    font-size: 0.5rem;
    width: 20%;
  }

  h5 {
    font-size: 0.7rem;
  }

  .nav {
    margin-right: 0;
    margin-top: 0;
  }
}

</style>   -->