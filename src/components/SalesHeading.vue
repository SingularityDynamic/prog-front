<script setup>

</script>
<template>
    <div class="headerBackground">
        <div class="headerText">
            <h1>Sales Reporting</h1>
        </div>
    </div>
    <div class="subHeader">
        <h2>Sale Breakdown</h2>
    </div>
</template>

<style scoped>
.headerText {
    grid-row: 1 / 1;
    grid-column: 1 / 3;
    margin-top: 2rem;
    margin-left: 1rem;
    color: #fafafa;
}

.headerBackground {
    grid-column: 1 / 7;
    display: grid;
    grid-template-columns: subgrid;
    grid-template-rows: subgrid;
    background-color: #000230;
}

.subHeader {
    grid-row: 2;
    grid-column: 2 / 4;
    display: grid;
    text-align: center;
    grid-template-rows: 1fr 1fr;
}

.subHeader h2 {
    grid-row: 2;
}
</style>