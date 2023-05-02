<template>
    <div class="loading position-absolute w-100 h-100">
        <div class="overlay"></div>
        <span class="load"></span>
    </div>
</template>
<script setup>

</script>
<style lang="scss" scoped>
.loading {
    left: 0;
    top: 0;
    display: flex;
    align-items: center;
    justify-content: center;
}

.overlay {
    position: absolute;
    height: 100%;
    width: 100%;
    left: 0;
    top: 0;
    background-color: black;
    opacity: 0.3;
}

.load {
    width: 100px;
    height: 100px;
    border: 10px solid var(--Very-Dark-Gray);
    border-top-color: transparent;
    border-radius: 50%;
    display: block;
    animation: spin 0.7s linear infinite;
}

@keyframes spin {
    0% {
        transform: rotate(360deg);
    }
}
</style>