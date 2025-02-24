<script setup>
const supabase = useSupabaseClient()
const barangART = ref([])
const keyword = ref('')
const categories = ref([])


const getBarang = async () => {
  const { data, error } = await supabase.from('barang').select(`*, kategori(*)`)
  .ilike('nama_barang', `%${keyword.value}%`).order("created_at", { ascending: false }).eq('id_kategori', 2);

  if (data) {
    barangART.value = data
    }
}

// const { data: barangATK, refresh } = useLazyAsyncData('barangATK', async () => {
//     const { data, error } = await supabase
//     .from('barang')
//     .select(`*, kategori(*)`)
//     .order("created_at", { ascending: false })
//     .eq('id_kategori', 1);
//     if (error) throw error
//     return (data)
// })

const getCategory = async () => {
    const { data, error } = await supabase.from('kategori').select('*')
    if (data) categories.value = data
}

const barangFiltered = computed(() => {
    return barangART.value?.filter((b) => {
        return (
            b.nama_barang?.toLowerCase().includes(keyword.value.toLowerCase()) || 
            b.created_at?.toLowerCase().includes(keyword.value.toLowerCase())
        )
    })
})


onMounted(() => {
    getCategory()
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
                <div class="row mt-5 p-0 title-search">
                    <div class="col-md-6">
                        <h3 class="mt-4">Barang ART</h3>
                    </div>
                    <div class="col-md-6 mt-4 d-flex justify-content-end">
                        <nuxt-link to="/barangART/add">
                            <button class="btn shadow rounded-4"><i class="bi bi-plus-circle fs-6"></i> Barang ART</button>
                        </nuxt-link>
                    </div>
                </div>                
                <div class="filter d-flex justify-content-end mt-5">
                    <form class="d-flex gap-4">
                        <input type="text" placeholder="Searh..." class="search rounded-5">
                        <input type="date" class="date rounded-5">
                    </form>
                </div>
                <div class="card card-riwayat mt-4 shadow">
                    <div class="table-responsive text-center mt-4">
                        <table class="mb-4">
                            <thead class="text-center">
                                <tr>
                                    <th>No</th>
                                    <th>Foto Barang</th>
                                    <th>Tanggal</th>
                                    <th>Nama Barang</th>
                                    <th>Jenis Barang</th>
                                    <th>Jumlah</th>
                                    <th>Edit</th>
                                    <th>Hapus</th>
                                </tr>
                            </thead>
                            <tbody class="text-center">
                                <tr v-for="(barang, i) in barangFiltered" :key="i">
                                    <td>{{ i + 1 }}</td>
                                    <td><img :src="barang.foto_barang" class="rounded-3"></td>
                                    <td>{{ barang.created_at }}</td>
                                    <td>{{ barang.nama_barang }}</td>
                                    <td>{{ barang.kategori?.nama }}</td>
                                    <td>{{ barang.jumlah }}</td>
                                    <td class="edit"><i class="bi bi-pencil-square"></i></td>
                                    <td class="delete"><i class="bi bi-trash3 text-danger"></i></td>
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

/* .container-fluid, .page, .content {
    margin: 0;
    padding: 0;
} */

h2, h3, h6, th, td, input {
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

th {
    background-color: #BCCCDC;
}

th, td {
    border: 0.5px solid #000;

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

.btn {
    width: 15rem;
    color: #fff;
    background-color: #9AA6B2;
    border: 0.5px solid #000;
}

table {
    width: 95%;
    margin: auto;
}

.edit, .delete {
    width: 4rem;
}

img {
    width: 5rem;

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

    th, td {
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

    .btn i {
        font-size: 0.7rem !important;
    }

    .btn {
        font-size: 0.5rem;
        padding-top: 0;
        width: 6rem;
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

    table i {
        font-size: 0.5rem;
    }

}

@media only screen and (min-width: 600px) and (max-width: 890px) {
    .title-search {
        padding-top: 1.5rem !important;
    }

    h3 {
        margin-left: 2rem;
        font-size: 1.3rem;
    }

    .content {
        margin-left: 6rem;
        margin-top: 3rem;
    }


    th, td {
        font-size: 1rem;
    }

    .btn {
        font-size: 0.9rem;
        padding-top: 0.1rem;
        width: 10rem;
        height: 2rem;
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
                    <div class="col-5">
                        <h3 class="mt-4">Barang ATK</h3>
                    </div>
                    <div class="col-7">
                        <div class="filter mt-3">
                            <form class="d-flex gap-4">
                                <input type="text" placeholder="Searh..." class="search shadow rounded-5">
                                <input type="date" class="date shadow rounded-5">
                                <button class="btn shadow rounded-4"><i class="bi bi-plus-circle fs-6"></i> Barang ATK</button>
                            </form>
                        </div>
                    </div>
                </div>                
                <div class="card card-barang mt-5 shadow">
                    <div class="text-center m-4">
                        <table class="mb-3 mt-3">
                                <thead class="text-center">
                                    <tr>
                                        <th>No</th>
                                        <th>Tanggal Penambahan</th>
                                        <th>Nama Barang</th>
                                        <th>Jumlah</th>
                                        <th>Sisa</th>
                                        <th>Edit</th>
                                        <th>Hapus</th>
                                    </tr>
                                </thead>
                                <tbody class="text-center">
                                    <tr>
                                        <td>1</td>
                                        <td>10 / 02 / 2025</td>
                                        <td>Pulpen</td>
                                        <td>1</td>
                                        <td>1</td>
                                        <td class="edit"><i class="bi bi-pencil-square"></i></td>
                                        <td class="delete"><i class="bi bi-trash3 text-danger"></i></td>
                                    </tr>
                                </tbody>
                                <tbody class="text-center">
                                    <tr>
                                        <td>2</td>
                                        <td>10 / 02 / 2025</td>
                                        <td>Kertas HVS</td>
                                        <td>1</td>
                                        <td>1</td>
                                        <td class="edit"><i class="bi bi-pencil-square"></i></td>
                                        <td class="delete"><i class="bi bi-trash3 text-danger"></i></td>
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

h2, h3, h6, th, td, .btn {
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

.card-barang {
    width: 90%;
    margin: auto;
}

table {
    width: 95%;
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
    border: 0.5px solid #000;
}

.date {
    width: 13rem;
    height: 2.5rem;
    color: #fff;
    background-color: #9AA6B2;
    border: 0.5px solid #000;
}

.filter {
    margin: end;
    margin-right: 50px;
}

.btn {
    width: 15rem;
    color: #fff;
    background-color: #9AA6B2;
    border: 0.5px solid #000;
}

.content {
    margin-left: 20rem; /* Offset the main content to the right of the sidebar */
    flex-grow: 1;
    margin-top: 5rem;
}

.edit, .delete {
    width: 4rem;
}

</style> -->