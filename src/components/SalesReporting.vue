<script setup>
import { ref } from "vue";
const model = ref({
  "sales": [
    {
        "salePrice": 0.00,
        "vehicleType": "Overall",
        "basicFee": 0.00,
        "specialFee": 0.00,
        "associationFee": 0.00,
        "storageFee": 0.00,
        "total": 0.00
    }
  ]
});
// Which values to display
const selected = ref(0);
const viewOverall = ref(false);
const loading = ref(false);

// Values to display
const feeSum = ref(0);

const newValue = ref('');
const vehicleType = ref('');

const errors = ref([]);
const sales = ref(0);


function addSale(processedSale) {
    model.value.sales.push(processedSale);
    sales.value++;
    updateOverall(processedSale);
    if (!viewOverall.value) {
        setSelected(model.value.sales.length - 1);
        feeSum.value = (model.value.sales[selected.value].basicFee + model.value.sales[selected.value].specialFee + model.value.sales[selected.value].associationFee + model.value.sales[selected.value].storageFee);
    }
    newValue.value = '';
    vehicleType.value = '';
}

function updateOverall(processedSale) {
    model.value.sales[0].basicFee += processedSale.basicFee;
    model.value.sales[0].specialFee += processedSale.specialFee;
    model.value.sales[0].associationFee += processedSale.associationFee;
    model.value.sales[0].storageFee += processedSale.storageFee;
    model.value.sales[0].salePrice += processedSale.salePrice;
    model.value.sales[0].total += processedSale.total;
}

function setSelected(newSelected) {
    if (newSelected == 0) {
        viewOverall.value = true;
    }
    else {
        viewOverall.value = false;
    }
    selected.value = newSelected;
}

async function getCalculation(vehicle, price) {
    if (price !== '' && vehicle !== '') {
        let fullSale = '';
        try {
            loading.value = true;
            let queryString = 'vehicle='+vehicle+'&price='+price;
            const res = await fetch('http://localhost:5059/pricing?'+queryString);
            fullSale = (await res.json());
            if (fullSale.statusCode == 200) {
                addSale(fullSale.saleDetails);
            }
        } catch (error) {
            errors.value.push('Error! Could not reach the API. ' + error);
        } finally {
            loading.value = false;
        }
    }
}
</script>

<template>
    <div class="menuHack">
        <details>
            <summary>
                Sales list
            </summary>
            <ol>
                <li v-for="(sale, index) in model.sales" :key="index">
                    <a @click="setSelected(index)">{{ sale.vehicleType }}: ${{ sale.salePrice }}<span v-if="index == selected" :id="index"> &lt;</span></a>
                </li>
            </ol>
        </details>
    </div>
    <div class="newEntry">
        <label class="saleLabel" id="addSaleLabel" for="salePrice">New sale: </label>
        <div id="inputs">
            <input id="salePrice" v-model="newValue" placeholder="0.00" />
            <div id="radios" class="radios">
                <div class="commonRadio">
                    <input id="vehicleComRadio" type="radio" v-model="vehicleType" value="Common" :selected="vehicleType == 'Common'"/>
                    <label id="commonLabel" for="vehicleComRadio"> Common</label>
                </div>
                <div class="luxuryRadio">
                    <input id="vehicleLuxRadio" type="radio" v-model="vehicleType" value="Luxury" :selected="vehicleType == 'Luxury'"/>
                    <label id="luxuryLabel" for="vehicleLuxRadio"> Luxury</label>
                </div>
            </div>
        </div>
        <button @click="getCalculation(vehicleType, newValue)">Add sale</button>
    </div>
    <div class="breakdown">
        <div v-if="loading" class="calculating">
            Calculating, please wait...
        </div>
        <div class="fees">
            <h3>Fee Breakdown</h3>
            <ul>
                <li id="basic">
                    <div id="basicFeeLabel">Basic fee:</div>
                    <div id="basicFeeValue" class="price">${{ model.sales[selected].basicFee.toFixed(2) }}</div>
                </li>
                <li id="special">
                    <div id="specialFeeLabel">Special fee:</div>
                    <div id="specialFeeValue" class="price">${{ model.sales[selected].specialFee.toFixed(2) }}</div>
                </li>
                <li id="association">
                    <div id="associationFeeLabel">Association fee:</div>
                    <div id="associationFeeValue" class="price">${{ model.sales[selected].associationFee.toFixed(2) }}</div>
                </li>
                <li id="storage">
                    <div id="storageFeeLabel">Storage fee:</div>
                    <div id="storageFeeValue" class="price">${{ model.sales[selected].storageFee.toFixed(2) }}</div>
                </li>
            </ul>
        </div>
        <div class="totals">
            <h3>Totals</h3>
            <ul>
                <li id="originalAmount">
                    <div id="originalAmountLabel">Original Sale:</div>
                    <div id="originalAmountValue" class="price">${{ model.sales[selected].salePrice.toFixed(2) }}</div>
                </li>
                <li id="feeTotal">
                    <div id="feeTotalLabel">Total fees:</div>
                    <div id="feeTotalValue" class="price">${{ feeSum.toFixed(2) }}</div>
                </li>
                <li id="overallTotal">
                    <div id="overallTotalLabel" class="total">Total:</div>
                    <div id="overallTotalValue" class="price total">${{ model.sales[selected].total.toFixed(2) }}</div>
                </li>
            </ul>
        </div>
    </div>
</template>

<style scoped>
.newEntry {
    grid-row: 1;
    grid-column: 3 / 5;
    display: flex;
    justify-content: space-around;
    align-items: center;
}

.radios {
    display: flex;
    flex-flow: row nowrap;
    justify-content: space-around;
}

.commonRadio {
    text-align: left;
}

.luxuryRadio {
    text-align: right;
}

.saleLabel {
    font-size: large;
}

.calculating {
    grid-column: 2 / 4;
    text-align: center;
    color: aquamarine;
}

.errorMessage {
    color: red;
    grid-column: 2 / 5;
    text-align: center;
}

.breakdown {
    grid-row: 2 / 5;
    grid-column: 2 / 6;
    display: grid;
    grid-template-rows: subgrid;
    grid-template-columns: subgrid;
}

.fees {
    grid-row: 2 / 5;
    grid-column: 2;
    line-height: 2em;
}

.totals {
    grid-row: 2 / 5;
    grid-column: 3;
    line-height: 2em;
}

/* Menu Section */

.menuHack {
    grid-column: 1;
    grid-row-start: 1;
}

.menuHack p {
    text-align: center;
}

.menuHack ol {
    padding-inline-start: 2.5em;
}

.menuhack ol li {
    list-style-type: none;
}

a {
    cursor: pointer;
}

summary {
    cursor: pointer;
    padding-inline-start: 1em;
}

h3 {
    text-align: center;
}

ul {
    padding-inline-start: 0;
}

ul li {
    display: grid;
    grid-template-columns: 1fr 1fr;
    list-style-type: none;
}

ul li .price {
    text-align: right;
    padding-right: 1em;
}

ul li .total {
    font-weight: 700;
    color: var(--vt-c-text-dark-3);
}
</style>