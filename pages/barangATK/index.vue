<script setup>
const supabase = useSupabaseClient()
const barangATK = ref([])
const keyword = ref('')
const selectBarang = ref({})
const categories = ref([])
const satuan = ref([])
const router = useRouter()
const limit = 10 
const offset = ref(0) 
const hasMore = ref(true)
const isPrinting = false


const goToEdit = (barangID) => {
    router.push(`/barangATK/${barangID}`)
}

function toggleDelete(barang) {
    selectBarang.value = barang
}

const getBarang = async () => {
    const { data, error } = await supabase
        .from('barang')
        .select(`*, kategori(*), satuan(*)`)
        .eq('id_kategori', 1)
        .order("created_at", { ascending: false })
        .range(offset.value, offset.value + limit - 1)

    if (error) {
        console.log(error)
        return
    }

    if (data.length < limit) {
        hasMore.value = false
    }

    barangATK.value.push(...data)
    offset.value += limit

}

const getCategory = async () => {
    const { data, error } = await supabase.from('kategori').select('*')
    if (data) categories.value = data
}

const getSatuan = async () => {
    const { data, error } = await supabase.from('satuan').select('*')
    if (data) satuan.value = data
}

const barangFiltered = computed(() => {
    return barangATK.value?.filter((b) => {
        return (
            b.nama_barang?.toLowerCase().includes(keyword.value.toLowerCase()) || 
            b.created_at?.toLowerCase().includes(keyword.value.toLowerCase())
        )
    })
})


const deleteBarang = async (id) => {
    const { error } = await supabase.from('barang').delete().eq('id', id)
    if (error) {
        console.error(error)
        return
    }

    // Reset daftar barang & ambil ulang data
    barangATK.value = []
    offset.value = 0
    hasMore.value = true
    await getBarang() // Muat ulang data dari awal
}

const getFormattedTodayDate = () => {
  const today = new Date();
  const day = String(today.getDate()).padStart(2, '0');
  const month = String(today.getMonth() + 1).padStart(2, '0');
  const year = today.getFullYear();
  return `${day}/${month}/${year}`;
};


const printTable = () => {
//   const navbar = document.getElementById('navbar').outerHTML; // Ambil isi navbar
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
            <h4 class="text-center mt-2 mb-4">BARANG ATK <br> UPA PERPUSTAKAAN UNIVERSITAS SILIWANGI</h4>
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
    getBarang()
    getCategory()
    getSatuan()
    // refresh()
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
                    <div class="col-6 col-md-6">
                        <h3 class="mt-4">Barang ATK</h3>
                    </div>
                    <div class="col-6 col-md-6 mt-4 d-flex justify-content-end">
                        <nuxt-link to="/barangATK/add">
                            <button class="btn shadow rounded-4"><i class="bi bi-plus-circle fs-6"></i> Barang ATK</button>
                        </nuxt-link>
                    </div>
                </div>
                <div class="row mt-5 title-search d-flex justify-content-end">
                    <div class="col-12 col-md-6">
                        <div class="filter">
                            <form @submit.prevent="getBarang" class="d-flex gap-4">
                                <input v-model="keyword" type="text" placeholder="Searh..." class="search rounded-5">
                                <input v-model="keyword" type="date" class="date rounded-5">
                            </form>
                        </div>
                    </div>
                    <div class="col-12 col-md-2">
                        <button type="button" class="btn btn-print rounded-5"  @click="printTable">Print</button>
                    </div>
                </div>
                <div class="card card-riwayat mt-4 shadow">
                    <div id="printableArea" class="table-responsive text-center mt-4 mb-5">
                        <table class="">
                            <thead class="text-center">
                                <tr>
                                    <th>No</th>
                                    <th>Foto Barang</th>
                                    <th>Tanggal</th>
                                    <th>Nama Barang</th>
                                    <th>Spek</th>
                                    <th>Jumlah</th>
                                    <th v-if="!isPrinting" class="th-edit">Edit</th>
                                    <th v-if="!isPrinting" class="th-delete">Hapus</th>
                                </tr>
                            </thead>
                            <tbody class="text-center">
                                <tr v-for="(barang, i) in barangFiltered" :key="i">
                                    <td>{{ i + 1 }}</td>
                                    <td><img :src="barang.foto_barang" class="rounded-3"></td>
                                    <td>{{ barang.created_at }}</td>
                                    <td>{{ barang.nama_barang }}</td>
                                    <td>{{ barang.spek }}</td>
                                    <td>{{ barang.jumlah }} {{ barang.satuan?.nama }}</td>
                                    <td v-if="!isPrinting" class="edit">
                                            <i class="bi bi-pencil-square" @click="goToEdit(barang.id)"></i>
                                    </td>
                                    <td v-if="!isPrinting" class="delete text-danger"><i class="bi bi-trash3 text-danger"data-bs-toggle="modal"
                                        data-bs-target="#deleteModal" @click="toggleDelete(barang)"></i></td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    <div class="text-center">
                        <button class="btn mb-3 rounded-5" v-if="hasMore" @click="getBarang"> Load More </button>
                    </div>
                </div>
            </div>
        </div>

        <!--Modal Delete-->
        <div class="modal fade center" id="deleteModal" tabindex="-1" aria-labelledby="deleteModalLabel"
            aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered modal-md p-4 rounded-5">
                <div class="modal-content">
                    <div class="modal-header">
                        <div class="modal-title fs-5" id="exampleModalLabel"></div>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body text-center">
                        <h5>Apakah anda yakin akan menghapus {{ selectBarang.nama_barang }}?</h5>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-edit rounded-5" data-bs-dismiss="modal">Batal</button>
                        <button type="button" class="btn btn-delete btn-danger rounded-5" data-bs-dismiss="modal"
                            @click="deleteBarang(selectBarang.id)">Hapus</button>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template> 

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,100..700;1,100..700&display=swap');

h2, h3, h5, h6, th, td, input, .btn-print {
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
    border: none;
}

table {
    width: 95%;
    margin: auto;
}

img {
    width: 5rem;

}

.edit, .delete {
    width: 4rem;
}

th, td {
    border: 0.5px solid #000;

}

.btn-print {
    color: #fff;
    width: 8rem;
    height: 2.5rem;
    background-color: #9AA6B2;
}

.btn-edit, .btn-delete {
    width: 12.5rem;
}

.btn-delete {
    background-color: red;
}

@media print {
    .edit, .delete, .th-edit, .th-delete {
        display: none !important;
    }
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
        font-size: 0.5rem;
    }

    .search {
        width: 7rem;
        height: 1rem;
    }

    .date {
        width: 4.5rem;
        height: 1rem;
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

    img {
        width: 2rem;

    }

    .btn-print {
        width: 4rem;
        height: 1rem;
        padding-top: 0.2rem;
        margin-top: -1rem;
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



<!-- <template>
    <div class="container-fluid">
        <div class="page d-flex">
            <Sidebar />
            <div class="content mb-5">
                <div class="row mt-5">
                    <div class="col-md-6 p-0">
                        <h3 class="mt-4">Barang ATK</h3>
                    </div>
                    <div class="col-md-6 d-flex justify-content-end">
                        <div class="filter mt-3">
                            <form class="d-flex gap-4">
                                <input type="text" placeholder="Searh..." class="search rounded-5">
                                <input type="date" class="date rounded-5">
                                <nuxt-link to="/barangATK/add">
                                    <button class="btn shadow rounded-4"><i class="bi bi-plus-circle fs-6"></i> Barang ATK</button>
                                </nuxt-link>
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
                                    <th>Edit</th>
                                    <th>Hapus</th>
                                </tr>
                            </thead>
                            <tbody class="text-center">
                                <tr>
                                    <td>1</td>
                                    <td>10 / 02 / 2025</td>
                                    <td>Indah</td>
                                    <td>Tisu</td>
                                    <td>ATK</td>
                                    <td>1</td>
                                    <td class="edit"><i class="bi bi-pencil-square"></i></td>
                                    <td class="delete"><i class="bi bi-trash3 text-danger"></i></td>
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

.btn {
    width: 15rem;
    color: #fff;
    background-color: #9AA6B2;
    border: 0.5px solid #000;
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

    .date, .btn {
        width: 5rem;
        height: 1.3rem;
    }

    .btn i {
        font-size: 0.7rem !important;
    }

    .btn {
        font-size: 0.5rem;
        padding-top: 0;
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

    table i {
        font-size: 0.5rem;
    }

}

</style> -->


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
                                        <td>ATK</td>
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