<script setup>
const supabase = useSupabaseClient()
const categories = ref([])
const barang = ref([])
const pengambil = ref([])
const keyword = ref('')
const jmlATK = ref()
const jmlART = ref()
const totalHariIni = ref(0)


const getHariIni = async () => {
    const today = new Date().toISOString().split('T')[0]
    const { count, error } = await supabase
    .from('riwayat') 
    .select('*', { count: 'exact', head: true }) 
    .gte('created_at', today) // Filter tanggal hari ini

  if (!error) {
    totalHariIni.value = count ?? 0
  } else {
    console.error('Gagal menghitung data:', error)
  }
}

const getjmlATK = async () => {
    const { data, count } = await supabase
        .from('barang')
        .select('*', { count: 'exact' })
        .eq("id_kategori", 1)
    if (data) jmlATK.value = count
}

const getjmlART = async () => {
    const { data, count } = await supabase
        .from('barang')
        .select('*', { count: 'exact' })
        .eq("id_kategori", 2)
    if (data) jmlART.value = count
}

const getRiwayat = async () => {
  const { data, error } = await supabase.from('riwayat').select(`*, barang(*), kategori(*)`)
  .ilike('nama_pengambil', `%${keyword.value}%`).order("created_at", { ascending: false });

  if (data) {
    pengambil.value = data
    }
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

onMounted(() => {
    getHariIni()
    getjmlART()
    getjmlATK()
    getRiwayat()
    getCategory()
    getBarang()
})
</script>

<template>
    <NavUser />
    <div class="container-fluid mb-5">
        <h3>Statistik</h3>
        <div class="statistik">
            <div class="row card-statistik justify-content-center mt-5 m-3">
                <div class="col-6 col-md-4">
                    <div class="card shadow rounded-4">
                        <div class="text m-3">
                            <div class="d-flex">
                                <span class="span-first text-center rounded-4"><i
                                        class="bi bi-card-list fs-2 mt-2"></i></span>
                                <span class="ms-auto text-center rounded-4"><i
                                        class="bi bi-arrow-up-left fs-5 arrow"></i></span>
                            </div>
                            <h6 class="mt-5">Total Pengambilan Hari Ini</h6>
                            <h2>{{ totalHariIni }}</h2>
                        </div>
                    </div>
                </div>
                <div class="col-6 col-md-4">
                    <div class="card shadow rounded-4">
                        <div class="text m-3">
                            <div class="d-flex">
                                <span class="span-first text-center rounded-4"><i
                                        class="bi bi-folder-fill fs-2 mt-2"></i></span>
                                <span class="ms-auto text-center rounded-4"><i
                                        class="bi bi-arrow-down-left fs-5 arrow"></i></span>
                            </div>
                            <h6 class="mt-5">Total Barang ATK</h6>
                            <h2>{{ jmlATK }}</h2>
                        </div>
                    </div>
                </div>
                <div class="col-6 col-md-4">
                    <div class="card shadow rounded-4 third">
                        <div class="text m-3">
                            <div class="d-flex">
                                <span class="span-first text-center rounded-4"><i
                                        class="bi bi-scissors fs-2 mt-2"></i></span>
                                <span class="ms-auto text-center rounded-4"><i
                                        class="bi bi-arrow-down-left fs-5 arrow"></i></span>
                            </div>
                            <h6 class="mt-5">Total Barang ART</h6>
                            <h2>{{ jmlART }}</h2>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="row mt-5">
            <div class="col-md-6">
                <h3 class="mt-4">Riwayat Pengambilan Barang</h3>
            </div>
            <div class="col-md-6 d-flex justify-content-end">
                <div class="filter mt-3">
                    <form @submit.prevent="getRiwayat" class="d-flex gap-4">
                        <input v-model="keyword" type="text" placeholder="Searh..." class="search rounded-5">
                        <input v-model="keyword" type="date" class="date rounded-5">
                    </form>
                </div>
            </div>
        </div>
        <div class="card card-riwayat mt-5 shadow">
            <div class="text-center mt-4">
                <table class="mb-4">
                    <thead class="text-center">
                        <tr>
                            <th>No</th>
                            <th>Tanggal</th>
                            <th>Nama Pengambil</th>
                            <th>Nama Barang</th>
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
                            <td>{{ pengambil.kategori?.nama }}</td>
                            <td>{{ pengambil.jumlah_pengambilan }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,100..700;1,100..700&display=swap');

.container-fluid {
    margin-top: 9rem;
}

h2,
h3,
h6,
th,
td,
input {
    font-family: "Josefin Sans", serif;
}

h3 {
    margin-left: 100px;
}

.arrow {
    color: green;
}

span {
    width: 5rem;
    height: 2rem;
    border: none;
    background-color: #EFFFE4;
}

.card-riwayat {
    width: 80%;
    margin: auto;
}

table {
    width: 90%;
    margin: auto;
}

.span-first {
    background-color: #ffff !important;
    border: 0.1px solid grey;
    height: 3.4rem;
    width: 4rem;
}

.span-first i {
    color: grey;
}

.search {
    width: 15rem;
    height: 2.5rem;
    border: 0.1px solid #000;
}

.date {
    width: 13rem;
    height: 2.5rem;
    color: #fff;
    background-color: #9AA6B2;
    border: 0.1px solid #000;
}

.filter {
    margin: end;
}

th {
    background-color: #BCCCDC;
}

@media only screen and (max-width: 600px) {
    h3 {
        margin-left: 1rem;
        margin-top: -3rem;
        font-size: 1rem;
    }

    .content {
        margin-left: 6rem;
        margin-top: 3rem;
    }

    .statistik {
        margin: 0 !important;
        margin-top: 1.5rem !important;
    }

    h6 {
        font-size: 0.7rem;
        margin-top: 1.5rem !important;
        font-weight: 300;
    }

    h2 {
        font-size: 1rem;
    }

    .third {
        margin-top: 1.5rem;
    }

    .span-first {
        height: 1.8rem;
        width: 2rem;
    }

    .span-first i {
        font-size: 1rem !important;
    }

    .arrow {
        font-size: 0.6rem !important;
    }

    span {
        width: 2rem;
        height: 1.7rem;
    }

    table {
        margin: 0 1rem;
    }

    th,
    td, .search, .date {
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
        gap: 0.5rem !important;
    }

    .filter {
        margin-top: 0.5rem !important;
    }

    .search::placeholder {
        font-size: 0.7rem !important;
        color: #888;
        padding-left: 0.5rem !important;
    }

    .date {
        font-size: 0.7rem !important;
    }

    .card-statistik {
        margin: 0.3rem;
        margin-top: 0 !important;
    }

    .col-4 {
        padding: 0.3rem;
    }
}

@media only screen and (min-width: 600px) and (max-width: 890px) {
    h3 {
        margin-left: 2rem;
        margin-top: 5rem;
        font-size: 1.3rem;
    }

    .content {
        margin-left: 6rem;
        margin-top: 3rem;
    }

    .statistik {
        margin: 2rem !important;
        margin-top: 1.5rem !important;
    }

    .span-first {
        height: 3rem;
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

}
</style>
