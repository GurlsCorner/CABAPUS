<script setup>
const supabase = useSupabaseClient()
const route = useRoute()
const barangID = Number(route.params.id)
const categories = ref([])
const satuan = ref([])
const imgBarang = ref()
const barangATK = ref({
    nama_barang: "",
    spek: "",
    id_kategori: null,
    jumlah: "",
    id_satuan: "",
    foto_barang: null
})


const getCategory = async () => {
    const { data, error } = await supabase.from('kategori').select('*')
    if (error) {
        console.error("Error fetching categories:", error)
        return
    }
    if (data) categories.value = data
}

const getSatuan = async () => {
    const { data, error } = await supabase.from('satuan').select('*')
    if (error) {
        console.error("Error fetching satuan:", error)
        return
    }
    if (data) satuan.value = data
}

const getBarangId = async () => {
    let { data, error } = await supabase
        .from('barang')
        .select(`*, kategori (*), satuan(*)`)
        .eq('id', barangID)
        .single()

    if (error) {
        console.log(this.$route
        );
        return
    }

    console.log("Barang ditemukan:", data)

    if (data.foto_barang)
        data.foto_barangPath = data.foto_barang.substr(data.foto_barang.indexOf(data.kategori.nama))

    barangATK.value = {
        ...data,
        id_kategori: data.kategori?.id || data.id_kategori
    };
}


async function imgPicked(e) {
    const file = e.target.files[0]
    imgBarang.value = file
    const { data: category, error } = await supabase.from('kategori')
        .select('nama, id')
        .eq('id', barangATK.value.kategori?.id || barangATK.value.id_kategori)
        .single();

    if (error) throw error;

    if (category && category.nama) {
        barangATK.value.foto_barangPath = `${category.nama}/${file.name}`;
        barangATK.value.id_kategori = category.id;
    } else {
        console.error("Kategori tidak ditemukan atau tidak memiliki nama!");
    }
}

const updateLoading = ref(false)

async function updateBarangATK() {
    try {
        updateLoading.value = true;

        let newFoto = barangATK.value.foto_barangPath;
        let oldFoto = barangATK.value.foto_barang?.substr(barangATK.value.foto_barang?.indexOf('fotoBarang'));


        // Jika ada gambar baru, lakukan update di storage
        if (imgBarang.value) {
            if (newFoto === oldFoto) {
                const { error } = await supabase.storage.from('fotoBarang').update(newFoto, imgBarang.value);
                if (error) throw error;
            } else {
                const { error } = await supabase.storage.from('fotoBarang').upload(newFoto, imgBarang.value);
                if (error) throw error;
                if (oldFoto) {
                    const { error } = await supabase.storage.from('fotoBarang').remove(oldFoto);
                    if (error) throw error;
                }
            }
        }

        const { data: { publicUrl: url } } = supabase.storage.from('fotoBarang').getPublicUrl(newFoto);


        const { error } = await supabase.from('barang').update({
            nama_barang: barangATK.value.nama_barang,
            spek: barangATK.value.spek,
            id_kategori: barangATK.value.id_kategori,
            jumlah: barangATK.value.jumlah,
            id_satuan: barangATK.value.id_satuan,
            foto_barang: url
        }).eq('id', barangID);

        if (error) throw error;

        console.log("Barang Berhasil Diperbarui!");
        navigateTo('/barangATK');

    } catch (error) {
        console.error(error);
    } finally {
        updateLoading.value = false;
    }
}


watch(() => route.params.id, (newID) => {
    if (newID) {
        console.log("ID baru ditemukan:", newID)
        getBarangId()
    }
}, { immediate: true }) // Agar dijalankan langsung saat halaman dimuat


onMounted(() => {
    getBarangId()
    getCategory()
    getSatuan()
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
                <h3>Edit Barang ATK </h3>
                <div class="row justify-content-center">
                    <div class="col-10 col-md-7">
                        <div class="card shadow rounded-4 m-5">
                            <form @submit.prevent="updateBarangATK" class="m-5">
                                <div class="mb-3">
                                    <img :src="barangATK.foto_barang" alt="img-barang" class="w-50"> <br>
                                     <p class="text-secondary"><i>Update gambar sedang dalam proses pengembangan</i></p>
                                    <input disabled @change="imgPicked" type="file"
                                        accept="image/*">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Nama Barang</label>
                                    <input v-model="barangATK.nama_barang" type="text" class="form-control">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Spek</label>
                                    <input v-model="barangATK.spek" type="text" class="form-control">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Kategori</label>
                                    <select v-model="barangATK.id_kategori"
                                        class="form-control form-select" id="keperluan">
                                        <option v-for="category in categories" :key="category.id_kategori"
                                            :value="category.id_kategori">
                                            {{ category.nama }}
                                        </option>
                                    </select>
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Satuan</label>
                                    <select v-model="barangATK.id_satuan"
                                        class="form-control form-select" id="keperluan">
                                        <option v-for="satu in satuan" :key="satu.id_satuan"
                                            :value="satu.id_satuan">
                                            {{ satu.nama }}
                                        </option>
                                    </select>
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Jumlah</label>
                                    <input v-model="barangATK.jumlah" type="number" class="form-control"
                                        aria-describedby="emailHelp">
                                </div>
                                <div class="add text-center mt-5">
                                    <button type="submit" class="btn rounded-5">Edit</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,100..700;1,100..700&display=swap');

h3,
p,
label,
input,
.btn {
    font-family: "Josefin Sans", serif;
}

h3 {
    margin-left: 2rem;
    margin-top: 4rem;
}

.page {
    display: flex;
}

.card {
    width: 80%;
    margin: auto;
}

.btn {
    width: 15rem;
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

    .card {
        width: 100%;
        margin: 2rem 0 !important;
    }

    .add {
        margin-top: 2rem !important;
    }

    .btn {
        padding-top: 0.4rem;
        width: 7rem;
        height: 1.7rem;
        font-size: 0.7rem;
    }

    form {
        margin: 2.5rem !important;
        font-size: 0.7rem;
    }

    .form-control {
        height: 1.5rem;
    }

    input {
        font-size: 0.7rem;
    }

}

@media only screen and (min-width: 600px) and (max-width: 890px) {
    h3 {
        margin-left: 2rem;
        margin-top: 5.5rem;
        font-size: 1.3rem;
    }

    .content {
        margin-left: 6rem;
        margin-top: 3rem;
    }

    .card {
        width: 100%;
        margin: 2rem 0 !important;
    }

    .submit {
        margin-top: 2rem !important;
    }

    .btn {
        padding-top: 0.4rem;
        width: 7rem;
        height: 1.7rem;
        font-size: 0.7rem;
    }

    form {
        margin: 2.5rem !important;
        font-size: 0.7rem;
    }

    .form-control {
        height: 1.5rem;
    }

    input {
        font-size: 0.7rem;
    }

}
</style>