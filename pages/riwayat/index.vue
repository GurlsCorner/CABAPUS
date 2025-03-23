<script setup>
const supabase = useSupabaseClient()
const categories = ref([])
const satuan = ref ([])
const barang = ref([])
const pengambil = ref([])
const keyword = ref('')

const getRiwayat = async () => {
  const { data, error } = await supabase.from('riwayat').select(`*, barang(*), kategori(*)`)
  .ilike('nama_pengambil', `%${keyword.value}%`).order("created_at", { ascending: false });

  if (data) {
    pengambil.value = data
    }
}

const getSatuan = async () => {
    const { data, error } = await supabase.from('satuan').select('*')
    if (data) satuan.value = data
}

const getCategory = async () => {
    const { data, error } = await supabase.from('kategori').select('*')
    if (data) categories.value = data
}

const getBarang = async () => {
    const { data, error } = await supabase.from('barang').select('*')
    if (data) barang.value = data
}

const pengambilFiltered = computed(() => {
    return pengambil.value?.filter((b) => {
        return (
            b.nama_pengambil?.toLowerCase().includes(keyword.value.toLowerCase()) ||
            b.created_at?.toLowerCase().includes(keyword.value.toLowerCase())
        )
    })
})


const getFormattedTodayDate = () => {
  const today = new Date();
  const day = String(today.getDate()).padStart(2, '0');
  const month = String(today.getMonth() + 1).padStart(2, '0');
  const year = today.getFullYear();
  return `${day}/${month}/${year}`;
};


const printTable = () => {
  const printContents = document.getElementById('printableArea').innerHTML; // Ambil isi tabel
  const formattedDate = getFormattedTodayDate();
  const tandaTangan = `
    <div class="d-flex mt-5">
        <div class="one">
            <h6 class="text-start mt-5">Pengelola Barang Persediaan</h6>
            <br><br>
            <h6 class="mt-5">............................................</h6>
        </div>
        <div class="two ms-auto text-start">
            <h6>Tasikmalaya, ${formattedDate}</h6>
            <h6>Mengetahui</h6>
            <h6>Kepala UPA Perpustakaan</h6>
            <br><br>
            <h6 class="mt-5">............................................</h6>
        </div>
    </div>
  `;
  const originalContents = document.body.innerHTML;

  document.body.innerHTML = `
    <html>
      <head>
        <title>Print</title>
        <style>
            h4 {
                font-family: "Josefin Sans", serif;
            }

            table, th, td {
                border: 1px solid black;
                border-collapse: collapse;
                text-align: center;
            }
        </style>
      </head>
      <body>
        <header>
            <h5 class="text-center mt-2 mb-4">RIWAYAT PENGAMBILAN BARANG <br> UPA PERPUSTAKAAN UNIVERSITAS SILIWANGI</h5>
        </header>
        ${printContents}
        ${tandaTangan}
      </body>
    </html>
  `;

  window.print();
  document.body.innerHTML = originalContents; 

  window.location.reload();
};


onMounted(() => {
    getRiwayat()
    getCategory()
    getSatuan()
    getBarang()
})


definePageMeta({
    layout: 'admin',
    middleware: 'auth'
})


</script>


<template>
    <div class="container-fluid">
        <div class="page d-flex">
            <Sidebar />
            <div class="content mb-5">
                <h3 class="mt-5">Riwayat Pengambilan Barang</h3>
                <div class="row mt-5 title-search d-flex justify-content-end">
                    <div class="col-md-6 justify-content-end">
                        <div class="filter">
                            <form @submit.prevent="getRiwayat" class="d-flex gap-4">
                                <input v-model="keyword" type="text" placeholder="Searh..." class="search rounded-5">
                                <input v-model="keyword" type="date" class="date rounded-5">
                            </form>
                        </div>
                    </div>
                    <div class="col-md-3">
                        <button type="button" class="btn btn-print rounded-5" @click="printTable">Print</button>
                    </div>
                </div>
                <div class="card card-riwayat mt-4 shadow">
                    <div id="printableArea" class="table-responsive text-center mt-4">
                        <table class="mb-4">
                            <thead class="text-center">
                                <tr>
                                    <th>No</th>
                                    <th>Tanggal</th>
                                    <th>Nama Pengambil</th>
                                    <th>Nama Barang</th>
                                    <th>Spek</th>
                                    <th>Jenis Barang</th>
                                    <th>Jumlah</th>
                                </tr>
                            </thead>
                            <tbody class="text-center">
                                <tr v-for="(pengambil, i) in pengambilFiltered" :key="i">
                                    <td>{{ i + 1 }}</td>
                                    <td>{{ pengambil.tanggal }}</td>
                                    <td>{{ pengambil.nama_pengambil }}</td>
                                    <td>{{ pengambil.barang?.nama_barang }}</td>
                                    <td>{{ pengambil.barang?.spek }}</td>
                                    <td>{{ pengambil.kategori?.nama }}</td>
                                    <td>{{ pengambil.jumlah_pengambilan }} {{ pengambil.barang?.id_satuan?.nama }}</td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,100..700;1,100..700&display=swap');

h2,
h3,
h6,
th,
td,
input, .btn-print {
    font-family: "Josefin Sans", serif;
}

h3 {
    margin-left: 2rem;
    margin-top: 4rem;
}

.card-riwayat {
    width: 90%;
    margin: auto;
}

table {
    width: 95%;
    margin: auto;
}

th {
    background-color: #BCCCDC;
}

.search {
    width: 15rem;
    height: 2.5rem;
}

.date {
    width: 13rem;
    height: 2.5rem;
    color: #fff;
    background-color: #9AA6B2;
}

.content {
    margin-left: 20rem;
    flex-grow: 1;
    margin-top: 5rem;
}

.btn-print {
    color: #fff;
    width: 10rem;
    height: 2.5rem;
    background-color: #9AA6B2;
    border: none;
}

.btn {
    border: none;

}

@media only screen and (max-width: 600px) {
    h3 {
        margin-left: 1rem;
        margin-top: 5rem !important;
        font-size: 1rem;
    }

    .content {
        margin-left: 6rem;
        margin-top: 3rem;
    }

    table {
        margin: 0 1rem;
        font-size: 0.5rem;
    }

    th,
    td {
        font-size: 0.6rem;
    }

    .search {
        width: 8rem;
        height: 1.3rem;
    }

    .date {
        width: 5rem;
        height: 1.3rem;
    }

    .filter .d-flex {
        margin-top: -2rem !important;
        gap: 0.5rem !important;
    }

    .search::placeholder {
        font-size: 0.7rem !important;
        color: #888;
        padding-left: 0.5rem !important;
    }

    .date {
        font-size: 0.7rem !important;
    }

    .btn-print {
        width: 4rem;
        height: 1rem;
        padding-top: 0.1rem;
        margin-top: 0.5rem;
        font-size: 0.7rem;
    }

}

@media only screen and (min-width: 600px) and (max-width: 890px) {
    h3 {
        margin-top: 6rem !important;
        margin-left: 2rem;
        font-size: 1.3rem;
    }

    .content {
        margin-left: 6rem;
        margin-top: 3rem;
    }

    th,
    td {
        font-size: 1rem;
    }

    .search {
        width: 11rem;
        height: 2rem;
    }

    .date {
        width: 8rem;
        height: 2rem;
    }

    .filter .d-flex {
        margin-top: 0 !important;
        padding: 0 !important;
        gap: 0.5rem !important;
    }

    .search::placeholder {
        font-size: 0.7rem !important;
        color: #888;
        padding-left: 1rem !important;
    }

    .date {
        font-size: 0.9rem !important;
    }

    .btn-print {
        width: 8rem;
        height: 2rem;
        padding-top: 0.2rem;
        padding: 0 !important;
    }

}
</style>
