<template>

    <UForm :validate="validate" :state="employee" class="space-y-4 flex" @submit="submitEmployee" @error="onError">
        <div class="w-full md:w-96">
            <div class="mb-2">
                <UFormGroup label="First Name" name="first_name">
                    <UInput placeholder="Enter First Name" v-model="employee.first_name" />
                </UFormGroup>
            </div>
            <div class="mb-2">
                <UFormGroup label="Last Name" name="last_name">
                    <UInput placeholder="Enter Last Name" v-model="employee.last_name" />
                </UFormGroup>
            </div>
            <div class="mb-2">
                <UFormGroup label="Mobile" name="mobile">
                    <UInput placeholder="Enter Mobile" v-model="employee.mobile" :maxlength="10"
                        @input="allowNumericValue" />
                </UFormGroup>
            </div>
            <div class="mb-2">
                <UFormGroup label="Pincode" name="pincode">
                    <UInput placeholder="Enter Pincode" :maxlength="6" v-model="employee.pincode" @change="checkPincode"
                        @input="allowNumericValue" />

                </UFormGroup>

            </div>
            <div class="mb-2">
                <UFormGroup label="State" name="state">
                    <UInput placeholder="Enter State" v-model="employee.state" readonly />
                </UFormGroup>
            </div>
            <div class="mb-2">
                <UFormGroup label="City" name="city">
                    <UInput placeholder="Enter City" v-model="employee.city" readonly />
                </UFormGroup>
            </div>
            <div class="mb-2">
                <UFormGroup name="Employment Type" label="Employment Type">
                    <UInputMenu v-model="employee.employe_type" :options="employeeTypes" />
                </UFormGroup>
            </div>
            <div class="mb-2">
                <UFormGroup :label="incomeTitle" name="income">
                    <UInput :placeholder="`Enter ${incomeTitle}`" v-model="employee.income" />
                </UFormGroup>
            </div>
            <div class="mb-2">
                <UButton type="submit">
                    Submit
                </UButton>
            </div>
        </div>
    </UForm>
</template>

<script setup lang="ts">
import type { FormError, FormErrorEvent } from '#ui/types'
const employee = ref({} as Employee);
const employeeTypes = [
    { label: 'Business', value: 'business' },
    { label: 'Salaried', value: 'salaried' }
];
let pincodeError = ref('')
let formChanged = ref(false)
const allowNumericValue = (event: InputEvent) => {
    const input = event.target as HTMLInputElement;
    input.value = input.value.replace(/\D/g, '');
    formChanged.value = true;
};

const validate = (employee: Employee): FormError[] => {
    const errors = []
    if (!employee.first_name) errors.push({ path: 'first_name', message: 'First Name Required' })
    if (!employee.last_name) errors.push({ path: 'last_name', message: 'Last Name Required' })

    if (!employee.employe_type) errors.push({ path: 'employe_type', message: 'Employee Type Required' })
    if (!employee.mobile) {
        errors.push({ path: 'mobile', message: 'Mobile Number Required' })
    } else if (!/^\d{10}$/.test(employee.mobile)) {
        errors.push({ path: 'mobile', message: 'Mobile Number should be  10 digits ' });
    }
    if (!employee.pincode) {
        errors.push({ path: 'pincode', message: 'Pincode Required' })
    }
    else if (!/^\d{6}$/.test(employee.pincode)) {
        errors.push({ path: 'pincode', message: 'Pincode should be  6 digits ' });
    }
    debugger;
    if (!employee.state) {
        errors.push({ path: 'pincode', message: 'Invalid pincode. Please enter a valid pincode.' })
    }
    if (!employee.income) errors.push({ path: 'income', message: 'Income Required' })

    formChanged.value = true;

    return errors
}

const incomeTitle = computed(() => {
    return employee.value.employe_type === 'Business' ? 'Income' : 'Salary';
});
function submitEmployee() {
    if (pincodeError.value === '') {
        sessionStorage.setItem('employee', JSON.stringify(employee.value));
        formChanged.value = false;
    }
}

async function onError(event: FormErrorEvent) {
    const element = document.getElementById(event.errors[0].id)
    element?.focus()
    element?.scrollIntoView({ behavior: 'smooth', block: 'center' })
}
async function checkPincode() {
    try {
        employee.value.state = ''
        employee.value.city = ''
        const response = await fetch(`https://api.postalpincode.in/pincode/${employee.value.pincode}`)
        const data = await response.json()

        if (data && data.length > 0) {
            const pincodeDetails = data[0]
            if (pincodeDetails.Status !== 'Error') {
                employee.value.state = pincodeDetails.PostOffice[0].State
                employee.value.city = pincodeDetails.PostOffice[0].District
            }
        }
        validate(employee.value)
    } catch (error) {
        console.error('Error checking pincode:', error)
        pincodeError.value = 'An error occurred while checking the pincode. Please try again later.'
    }

}
onBeforeRouteLeave((to, from, next) => {
    if (formChanged.value) {
        const confirmed = window.confirm(
            "Changes in the form. Do you want to save them before leaving?"
        );
        if (confirmed) {
            submitEmployee()
            next();
        } else {
            next(false); // Prevent leaving the route
        }
    } else {
        next();
    }
})
onMounted(() => {
    if (sessionStorage && sessionStorage.getItem('employee')) {
        const employeeData: string = sessionStorage.getItem('employee') || ''
        employee.value = JSON.parse(employeeData)
    }
})
</script>
