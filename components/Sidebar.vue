<template>
    <div class="container-fluid shadow">
        <div class="menu">
            <div class="card m-2 mt-3 rounded-4">
              <nuxt-link to="/dashboard" class="no-underline">
              <div class="d-flex m-3">
                <span class="span-first text-center rounded-3 mt-1"><i class="bi bi-house-door"></i></span>
                  <h6 class="mt-3">Dashboard</h6>
                </div>
              </nuxt-link>
            </div>
            <div class="card m-2 mt-4 rounded-4">
              <nuxt-link to="/formPengambilan" class="no-underline">
              <div class="d-flex m-3">
                <span class="span-first text-center rounded-3 mt-1"><i class="bi bi-card-list"></i></span>
                  <h6 class="mt-3">Form Pengambilan</h6>
                </div>
              </nuxt-link>
            </div>
            <div class="card m-2 mt-4 rounded-4 ">
              <nuxt-link to="/riwayat" class="no-underline">
              <div class="d-flex m-3">
                <span class="span-first text-center rounded-3 mt-1"><i class="bi bi-clock-history"></i></span>
                  <h6 class="mt-2">Riwayat Pengambilan</h6>
                </div>
              </nuxt-link>
              </div>
            <div class="card m-2 mt-4 rounded-4">
              <nuxt-link to="/barangATK" class="no-underline">
              <div class="d-flex m-3">
                <span class="span-first text-center rounded-3 mt-1"><i class="bi bi-folder-fill"></i></span> 
                  <h6 class="mt-3">Barang ATK</h6>
                </div>
              </nuxt-link>          
            </div>
            <div class="card m-2 mt-4 mb-5 rounded-4">
              <nuxt-link to="/barangART" class="no-underline">
              <div class="d-flex m-3">
                <span class="span-first text-center rounded-3 mt-1"><i class="bi bi-scissors"></i></span>  
                  <h6 class="mt-3">Barang ART</h6>
                </div>
              </nuxt-link>        
              </div>
        </div>
    </div>
</template>

<style scoped>
.container-fluid {
  height: 100%;
  width: 20rem; /* Pastikan sidebar punya lebar tetap */
  flex-shrink: 0; /* Mencegah sidebar mengecil */
  position: fixed;
  top: 7rem;
  left: 0;
  overflow: hidden;        
}

.menu {
  overflow-y: auto;
  max-height: 80vh;
  scrollbar-width: none; /* Firefox */
}

.menu::-webkit-scrollbar {
  display: none; /* Chrome, Safari, Edge */
}

h6 {
    font-family: "Josefin Sans", serif;
}

.card {
  margin: auto;
}

i {
  font-size: 1.8rem;
  color: #000;
}

h6 {
  margin-left: 20px;
  font-size: 1.2rem;
  color: #000;
}

.menu .d-flex {
  margin-left: 15px;
}

.span-first {
    background-color: #F8FAFC !important;
    border: 0.1px solid grey;
    width: 3.5rem;
    height: 3.2rem;
}

.no-underline {
  text-decoration: none;
}

@media only screen and (max-width: 600px) {
  .container-fluid {
    top: 6rem;
    width: 6rem; /* Pastikan sidebar punya lebar tetap */
    flex-shrink: 0; /* Mencegah sidebar mengecil */
  }

  .menu {
    max-height: 100vh;
  }

  i {
    font-size: 1.4rem;
  }

  h6 {
    display: none;
  }

  .card {
    margin: 0 !important;
    margin-top: 1rem !important;
  }

  .span-first {
    width: 3rem;
    height: 2.3rem;
    margin-top: 0 !important;
  }

  .d-flex {
    margin: 0.8rem !important;
  }

}

@media only screen and (min-width: 600px) and (max-width: 890px) {
  .container-fluid {
    top: 6rem;
    width: 6rem; /* Pastikan sidebar punya lebar tetap */
    flex-shrink: 0; /* Mencegah sidebar mengecil */
  }

  .menu {
    max-height: 100vh;
  }

  i {
    font-size: 1.4rem;
  }

  h6 {
    display: none;
  }

  .card {
    margin: 0 !important;
    margin-top: 1rem !important;
  }

  .span-first {
    width: 3rem;
    height: 2.3rem;
    margin-top: 0 !important;
  }

  .d-flex {
    margin: 0.8rem !important;
  }

}

</style>