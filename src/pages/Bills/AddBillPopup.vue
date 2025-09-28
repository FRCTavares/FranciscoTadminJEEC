<script setup>
import { ref } from 'vue';

import * as HttpAdmin from "@utils/http-admin";

const props = defineProps({
    isOpen: Boolean,
});

const emit = defineEmits(["modal-close","modal-submit"]);

async function submitAddBill(e) {
    e.preventDefault();
    const formData = new FormData();
    formData.append("bill_image_binary", bill_image_binary.value);
    formData.append("is_paid", is_paid.value);
    formData.append("status", status.value);
    formData.append("shop", shop.value);
    formData.append("description", description.value);
    formData.append("date", date.value);
    formData.append("value", value.value);
    

    const response = await HttpAdmin.POST("/insert-bill", formData);
    if(response.request.status != 200){ 
        alert("Something went wrong when creating a new bill...")
    }
    emit('modal-close');
    emit('modal-submit');
};

function handleFileChange(e) {
    const file = e.target.files[0];
    
    if (file){
        bill_image_binary.value = file;
    };
    
}

let value = ref();
let date = ref();
let shop = ref();
let description = ref();
let status = ref('Pending');
let is_paid = ref('No');
let bill_image_binary = ref();


</script>



<template>
<div class="modal-mask" v-if="props.isOpen"> 
    <div class="popup">
        <form class="form" @submit.prevent="submitAddBill">
            <h2>Add Bill</h2>
            
            <div class="form-grid">
                <!-- Row 1: Value and Shop -->
                <div class="form-group">
                    <label for="value">Value</label>
                    <input 
                        required 
                        type="number" 
                        step="0.01" 
                        min="0" 
                        placeholder="0.00" 
                        id="value" 
                        v-model="value"
                    >
                </div>
                
                <div class="form-group">
                    <label for="shop">Shop</label>
                    <input 
                        required 
                        type="text" 
                        placeholder="Enter shop name" 
                        id="shop" 
                        v-model="shop"
                    >
                </div>
                
                <!-- Row 2: Description (full width) -->
                <div class="form-group full-width">
                    <label for="description">Description</label>
                    <textarea 
                        id="description" 
                        v-model="description"
                        placeholder="Enter bill description"
                        rows="4"
                    ></textarea>
                </div>
                
                <!-- Row 3: Date and Bill Photo -->
                <div class="form-group">
                    <label for="date">Date</label>
                    <input 
                        required 
                        type="date" 
                        id="date" 
                        v-model="date"
                    >
                </div>
                
                <div class="form-group">
                    <label for="bill-photo">Bill Photo</label>
                    <div class="file-upload">
                        <button 
                            type="button" 
                            class="add-photo-btn"
                            @click="$refs.fileInput.click()"
                        >
                            Add Photo
                        </button>
                        <input 
                            type="file" 
                            ref="fileInput"
                            id="bill-photo"
                            accept="image/*" 
                            @change="handleFileChange"
                            style="display: none;"
                        >
                    </div>
                </div>
            </div>
            
            <div class="form-actions">
                <button 
                    class="cancel-btn" 
                    type="button"
                    @click="emit('modal-close')"
                >
                    Cancel
                </button>
                <button 
                    class="add-btn" 
                    type="submit"
                >
                    Add
                </button>
            </div>
        </form>
    </div>
</div>
</template>



<style scoped>
.modal-mask {
    position: fixed;
    z-index: 9999;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
}

.popup {
    background-color: white;
    border-radius: 16px;
    padding: 2rem;
    width: 90%;
    max-width: 600px;
    max-height: 90vh;
    overflow-y: auto;
    box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
}

.form h2 {
    color: var(--c-ft-dark);
    font-size: 1.5rem;
    font-weight: 600;
    margin-bottom: 2rem;
    text-align: left;
}

.form-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.5rem;
    margin-bottom: 2rem;
}

.form-group {
    display: flex;
    flex-direction: column;
}

.form-group.full-width {
    grid-column: 1 / -1;
}

.form-group label {
    color: var(--c-ft-dark);
    font-size: 1rem;
    font-weight: 500;
    margin-bottom: 0.5rem;
}

.form-group input,
.form-group textarea {
    padding: 0.75rem 1rem;
    border: 1.5px solid #e1e1e1;
    border-radius: 8px;
    font-size: 1rem;
    color: var(--c-ft-dark);
    background-color: white;
    outline: none;
    transition: border-color 0.2s ease;
}

.form-group input:focus,
.form-group textarea:focus {
    border-color: var(--c-select);
}

.form-group input::placeholder,
.form-group textarea::placeholder {
    color: var(--c-ft-light);
}

.form-group textarea {
    resize: vertical;
    min-height: 80px;
}

.file-upload {
    display: flex;
    align-items: center;
}

.add-photo-btn {
    background-color: var(--c-select);
    color: white;
    border: none;
    border-radius: 8px;
    padding: 0.75rem 1.5rem;
    font-size: 1rem;
    font-weight: 500;
    cursor: pointer;
    transition: background-color 0.2s ease;
}

.add-photo-btn:hover {
    background-color: #4088c4;
}

.form-actions {
    display: flex;
    justify-content: space-between;
    gap: 1rem;
}

.cancel-btn {
    background-color: #f3f4f6;
    color: var(--c-ft-dark);
    border: 1px solid #d1d5db;
    border-radius: 8px;
    padding: 0.875rem 2rem;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s ease;
}

.cancel-btn:hover {
    background-color: #e5e7eb;
    border-color: #9ca3af;
}

.add-btn {
    background-color: #1e3a8a;
    color: white;
    border: none;
    border-radius: 8px;
    padding: 0.875rem 2rem;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: background-color 0.2s ease;
}

.add-btn:hover {
    background-color: #1d4ed8;
}

@media (max-width: 768px) {
    .popup {
        width: 95%;
        padding: 1.5rem;
    }
    
    .form-grid {
        grid-template-columns: 1fr;
        gap: 1rem;
    }
    
    .form-group.full-width {
        grid-column: 1;
    }
    
    .form h2 {
        font-size: 1.25rem;
        margin-bottom: 1.5rem;
    }
}
</style>