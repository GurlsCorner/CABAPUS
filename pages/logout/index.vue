<script setup>
definePageMeta({
    layout: 'login'
})
const supabase = useSupabaseClient()

async function logout() {
    const { error } = await supabase.auth.signOut()
    if(!error) {
        navigateTo("/login")

    }
}

onMounted(() => {
    logout()
})

</script>


<template>
    <div class="container-fluid">
        <div class="text-center">
            <h2>Keluar dari aplikasi...</h2>
        </div>
    </div>
</template>


<style scoped>
.text-center {
    font-family: "Josefin Sans", serif;
}
</style>