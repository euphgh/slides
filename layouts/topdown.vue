<template>
    <div class="slidev-layout default">
        <slot />
        <div grid="~ cols-2" class="">
            <img src="/topdown/ooo-cpu.png" class="size-1/2"/>
            <div grid="~ rows-2">
                <img src="/mbapis/topdown.png" class="size-1/2"/>
                <img src="/topdown/top-break.png" class="size-1/2"/>
            </div>
        </div>
    </div>
</template>