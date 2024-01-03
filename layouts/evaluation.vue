<template>
    <div class="grid grid-cols-2 w-full h-full auto-rows-fr">
        <div class="slidev-layout default">
            <slot />
        </div>
        <div grid="~ rows-3">
            <img src="/mbapis/simpoint_est.png" />
            <img src="/mbapis/dcache_est.png" />
            <img src="/mbapis/mispredict_est.png" class="pb-4"/>
        </div>
    </div>
</template>