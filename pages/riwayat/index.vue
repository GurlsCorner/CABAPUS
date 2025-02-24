<script setup>
const supabase = useSupabaseClient()
const categories = ref([])
const pengambil = ref([])
const keyword = ref('')

const getRiwayat = async () => {
  const { data, error } = await supabase.from('riwayat').select(`*, kategori(*)`)
  .ilike('nama_pengambil', `%${keyword.value}%`).order("created_at", { ascending: false });

  if (data) {
    pengambil.value = data
    }
}

const getCategory = async () => {
    const { data, error } = await supabase.from('kategori').select('*')
    if (data) categories.value = data
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
    getRiwayat()
    getCategory()
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
                <div class="row mt-5 title-search">
                    <div class="col-md-6 p-0">
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
                <div class="card card-riwayat mt-4 shadow">
                    <div class="table-responsive text-center mt-4">
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
                                    <td>{{ pengambil.nama_barang }}</td>
                                    <td>{{ pengambil.kategori?.nama }}</td>
                                    <td>{{ pengambil.jumlah_pengambilan }}</td>
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
input {
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

@media only screen and (max-width: 600px) {
    h3 {
        margin-left: 1rem;
        margin-top: 5rem;
        font-size: 1rem;
    }

    .content {
        margin-left: 6rem;
        margin-top: 3rem;
    }

    table {
        margin: 0 1rem;
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

}

@media only screen and (min-width: 600px) and (max-width: 890px) {
    .title-search {
        padding-top: 1.5rem;
    }

    h3 {
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


<!-- <template>
    <div class="container-fluid">
        <div class="dashboard d-flex">
            <Sidebar />
            <div class="content mb-5">
                <div class="row mt-5">
                    <div class="col-md-6">
                        <h3 class="mt-4">Riwayat Peminjaman</h3>
                    </div>
                    <div class="col-md-6">
                        <div class="filter mt-3">
                            <form class="d-flex gap-4">
                                <input type="text" placeholder="Searh..." class="search rounded-5">
                                <input type="date" class="date rounded-5">
                            </form>
                        </div>
                    </div>
                </div>                  
                <div class="card card-riwayat mt-5 shadow">
                        <div class="table-responsive text-center mt-4">
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
                                    <tr>
                                        <td>1</td>
                                        <td>10 / 02 / 2025</td>
                                        <td>Indah</td>
                                        <td>Tisu</td>
                                        <td>ART</td>
                                        <td>1</td>
                                    </tr>
                                </tbody>
                                <tbody class="text-center">
                                    <tr>
                                        <td>2</td>
                                        <td>10 / 02 / 2025</td>
                                        <td>Fani</td>
                                        <td>Pulpen</td>
                                        <td>ATK</td>
                                        <td>1</td>
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

h2, h3, h6, th, td {
    font-family: "Josefin Sans", serif;
}

h3 {
    margin-left: 2rem;
    margin-top: 4rem;
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

.filter {
    margin: end;
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

.content {
    margin-left: 20rem;
    flex-grow: 1;
    margin-top: 5rem;
}

@media only screen and (max-width: 600px) {
    h3 {
        margin-left: 2rem;
        margin-top: 2rem !important;
    }

    .content {
        margin-left: 6rem;
        margin-top: 3rem;
    }

    table {
        margin: 0 1rem;
    }

    .search {
        width: 7rem;
        height: 1.8rem;
    }

    .date {
        width: 5rem;
        height: 1.8rem;
    }

    .filter {
        margin: end;
    }
}

</style> -->

<!-- <template>
    <div class="container-fluid mb-5">
        <div class="page d-flex">
            <Sidebar />
            <div class="content mb-5">
                <div class="row mt-5">
                    <div class="col-md-6 p-0">
                        <h3 class="mt-4">Riwayat Peminjaman</h3>
                    </div>
                    <div class="col-md-6">
                        <div class="filter mt-3">
                            <form class="d-flex gap-4">
                                <input type="text" placeholder="Searh..." class="search rounded-5">
                                <input type="date" class="date rounded-5">
                            </form>
                        </div>
                    </div>
                </div>                
                <div class="card card-riwayat mt-5 shadow">
                    <div class="table-responsive text-center mt-4">
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
                                <tr>
                                    <td>1</td>
                                    <td>10 / 02 / 2025</td>
                                    <td>Indah</td>
                                    <td>Tisu</td>
                                    <td>ART</td>
                                    <td>1</td>
                                </tr>
                            </tbody>
                            <tbody class="text-center">
                                <tr>
                                    <td>2</td>
                                    <td>10 / 02 / 2025</td>
                                    <td>Fani</td>
                                    <td>Pulpen</td>
                                    <td>ATK</td>
                                    <td>1</td>
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

h2, h3, h6, th, td {
    font-family: "Josefin Sans", serif;
}

h3 {
    margin-left: 2rem;
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
    width: 90%;
    margin: auto;
}

table {
    width: 90%;
    margin: auto;
}

.span-first {
    background-color: #ffff !important;
    border: 0.1px solid grey;
    height: 3.5rem;
}

.span-first i {
    color: grey;
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

.filter {
    margin: end;
}

.content {
    margin-left: 20rem; /* Offset the main content to the right of the sidebar */
    flex-grow: 1;
    margin-top: 5rem;
}

@media only screen and (max-width: 600px) {
    h3 {
        margin-left: 2rem;
        margin-top: 5rem;
    }

    .content {
        margin-left: 6rem;
        margin-top: 3rem;
    }
    .search {
        width: 8rem;
        height: 2rem;
    }

    .date {
        width: 5rem;
        height: 2rem;
    }

    table {
        margin: 0 1rem;
    }
}

</style>

<template>
    <div class="container-fluid mb-5">
        <div class="row mt-5">
            <div class="col-6">
                <h3 class="">Riwayat Peminjaman</h3>
            </div>
            <div class="col-6">
                <div class="filter">
                    <form class="d-flex gap-4">
                        <input type="text" placeholder="Searh..." class="search rounded-5">
                        <input type="date" class="date rounded-5">
                    </form>
                </div>
            </div>
        </div>
        <div class="card card-riwayat mt-5 shadow">
            <table class="mb-5">
                <div class="text-center m-4">
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
                        <tr>
                            <td>1</td>
                            <td>10 / 02 / 2025</td>
                            <td>Indah</td>
                            <td>Tisu</td>
                            <td>ART</td>
                            <td>1</td>
                        </tr>
                    </tbody>
                    <tbody class="text-center">
                        <tr>
                            <td>2</td>
                            <td>10 / 02 / 2025</td>
                            <td>Fani</td>
                            <td>Pulpen</td>
                            <td>ATK</td>
                            <td>1</td>
                        </tr>
                    </tbody>
                </div>
            </table>
        </div>
    </div>
</template> 

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,100..700;1,100..700&display=swap');

h2, h3, h6, th, td, input {
    font-family: "Josefin Sans", serif;
}

h3 {
    margin-left: 100px;
}

.btn {
    width: 10rem;
}

.card-riwayat {
    width: 90%;
    margin: auto;
}

table {
    width: 90%;
    margin: auto;
}

.span-first {
    background-color: #ffff !important;
    border: 0.1px solid grey;
    height: 4rem;
}

.span-first i {
    color: grey;
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

.filter {
    margin: end;
}
</style> -->