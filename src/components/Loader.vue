<template>
    <div class="loader__wrap">
        <div class="loader"></div>
    </div>
</template>

<style scoped lang="scss">
    .loader {
        border: 8px solid #f3f3f3; /* светлый фон */
        border-top: 8px solid #3498db; /* цвет верха (анимации) */
        border-radius: 50%;
        width: 60px;
        height: 60px;
        animation: spin 1s linear infinite;
        &__wrap{
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%,-50%);
        }
    }

    @keyframes spin {
        0% { transform: rotate(0deg); }
        100% { transform: rotate(360deg); }
    }
</style>
