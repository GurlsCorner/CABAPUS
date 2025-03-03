<template>
    <nav class="navbar navbar-expand-lg bg-light p-2 shadow fixed-top" id="navbar">
        <div class="container-fluid d-flex">
            <nuxt-link to="/dashboard" class="d-flex tohome">
                <img src="../assets/img/logo.png" alt="logo" class="logo mt-2">
                <div class="text mt-2">
                    <h4 class="fw-medium">CABAPUS</h4>
                    <p class="m-0">Catatan Barang Perpustakaan Universitas Siliwangi</p>
                </div>
            </nuxt-link>
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
    
  }
  
  </style>
  