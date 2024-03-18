<template>
    <UForm :validate="validate" :state="state" class="space-y-4" @submit="onSubmit" @error="onError">
        <div class="w-96">
            <div class="min-w-80 mb-2">
                <UFormGroup label="First Name" name="first_name">
                    <UInput placeholder="Enter First Name" v-model="state.first_name" />
                </UFormGroup>
            </div>
            <div class="min-w-80 mb-2">
                <UFormGroup label="Last Name" name="last_name">
                    <UInput placeholder="Enter Last Name" v-model="state.last_name" />
                </UFormGroup>
            </div>
            <div class="min-w-80 mb-2">
                <UFormGroup label="Mobile" name="mobile">
                    <UInput placeholder="Enter Mobile" v-model="state.mobile" :maxlength="10"
                        @input="allowNumericValue" />
                </UFormGroup>
            </div>
            <div class="min-w-80 mb-2">
                <UFormGroup label="Pincode" name="pincode">
                    <UInput placeholder="Enter Pincode" :maxlength="6" v-model="state.pincode" @change="checkPincode"
                        @input="allowNumericValue" />
                    <span v-if="pincodeError" class="text-red-500">{{ pincodeError }}</span>
                </UFormGroup>
            </div>
            <div class="min-w-80 mb-2">
                <UFormGroup name="Employment Type" label="Employment Type">
                    <UInputMenu v-model="state.employe_type" :options="people" />
                </UFormGroup>
            </div>
            <div class="min-w-80 mb-2">
                <UFormGroup :label="incomeTitle" name="income">
                    <UInput :placeholder="`Enter ${incomeTitle}`" v-model="state.income" />
                </UFormGroup>
            </div>
            <div class="min-w-80 mb-2">
                <UButton type="submit">
                    Submit
                </UButton>
            </div>
        </div>
    </UForm>
</template>

<script setup lang="ts">
import type { FormError, FormErrorEvent, FormSubmitEvent } from '#ui/types'
const people = ['Business', 'Salaried']
const state = ref({
    first_name: undefined,
    last_name: undefined,
    mobile: undefined,
    pincode: undefined,
    employe_type: people[0],
    income: undefined,
})
let pincodeError = ref('')
const allowNumericValue = (event: InputEvent) => {
    const input = event.target as HTMLInputElement;
    input.value = input.value.replace(/\D/g, ''); // Remove non-numeric characters
};
const validate = (state: any): FormError[] => {
    const errors = []
    if (!state.first_name) errors.push({ path: 'first_name', message: 'First Name Required' })
    if (!state.last_name) errors.push({ path: 'last_name', message: 'Last Name Required' })

    if (!state.employe_type) errors.push({ path: 'employe_type', message: 'Employee Type Required' })
    if (!state.mobile) {
        errors.push({ path: 'mobile', message: 'Mobile Number Required' })
    } else if (!/^\d{10}$/.test(state.mobile)) {
        errors.push({ path: 'mobile', message: 'Mobile Number should be  10 digits ' });
    }
    if (!state.pincode) {
        errors.push({ path: 'pincode', message: 'Pincode Required' })
    } else if (!/^\d{6}$/.test(state.pincode)) {
        errors.push({ path: 'pincode', message: 'Mobile Number should be  6 digits ' });
    }
    return errors
}
const incomeTitle = computed(() => {
    return state.value.employe_type === 'Business' ? 'Income' : 'Salary';
});
async function onSubmit(event: FormSubmitEvent<any>) {
    // Do something with data
    sessionStorage.setItem('employee', JSON.stringify(event.data));
}

async function onError(event: FormErrorEvent) {
    const element = document.getElementById(event.errors[0].id)
    element?.focus()
    element?.scrollIntoView({ behavior: 'smooth', block: 'center' })
}
async function checkPincode() {
    try {
        const response = await fetch(`https://api.postalpincode.in/pincode/${state.value.pincode}`)
        const data = await response.json()
        if (data && data.length > 0) {
            const pincodeDetails = data[0]
            if (pincodeDetails.Status === 'Error') {
                pincodeError.value = 'Invalid pincode. Please enter a valid pincode.'
            } else {
                pincodeError.value = ''
            }
        } else {
            pincodeError.value = 'Invalid pincode. Please enter a valid pincode.'
        }
    } catch (error) {
        console.error('Error checking pincode:', error)
        pincodeError.value = 'An error occurred while checking the pincode. Please try again later.'
    }

}
onMounted(() => {
    if (sessionStorage && sessionStorage.getItem('employee') && typeof sessionStorage.getItem('employee') === 'string') {
        const employee: string = sessionStorage.getItem('employee') || ''
        state.value = JSON.parse(employee)
    }
})
</script>
