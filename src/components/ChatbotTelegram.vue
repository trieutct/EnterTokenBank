<template>
    <v-container class="w-50 h-50">
        <v-text-field :disabled="tokentx !== ''" v-model="tokentx" variant="outlined" label="Nhập token"
            required></v-text-field>
        <v-btn @click="submit" color="primary">Submit</v-btn>
    </v-container>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import { URL } from "@/common/constants.ts";
import axios from "axios";
const tokentx = ref<string>("");
const tokenExpriedCount = ref<number>(0);
async function submit() {
    if (tokentx.value) {
        try {
            await getbank();
            await getbankd();
        } catch (error) {
            console.error("An error occurred:", error);
            tokentx.value = "";
            tokenExpriedCount.value = 0;
        }
    } else {
        if (tokenExpriedCount.value < 2) {
            await tokenExpried();
            tokenExpriedCount.value += 1;
        }
    }
}

async function getbank() {
    try {
        const res = await axios.get(`${URL}/getbank`, {
            params: {
                tokentx: tokentx.value,
            },
        });
        if (res.status !== 200) {
            console.log("getbank lỗi");
            throw new Error("getbank failed");
        }
        console.log("getBank");
        console.log(res);
    } catch (error) {
        console.log("getbank lỗi");
        console.error("Error in getbank:", error);
        throw error;
    }
}

async function getbankd() {
    try {
        const res = await axios.get(`${URL}/getbankd`, {
            params: {
                tokentx: tokentx.value,
            },
        });
        if (res.status !== 200) {
            throw new Error("getbankd failed");
        }
        console.log("getbankd");
        console.log(res);
    } catch (error) {
        console.log("getbankd lỗi");
        console.error("Error in getbankd:", error);
        throw error;
    }
}
async function tokenExpried() {
    try {
        const res = await axios.get(`${URL}/tokenExpried`);
        console.log("token hết hạn");
        console.log(res);
    } catch (error) {
        console.log("token hết hạn lỗi");
        console.error("Error in getbankd:", error);
        throw error;
    }
}

onMounted(() => {
    const interval = setInterval(submit, 10000);
    onUnmounted(() => {
        clearInterval(interval);
    });
});
</script>