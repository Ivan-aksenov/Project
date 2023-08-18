<script>
export default {
  data() {
    return {
      searchName: '',
      searchDept: '', 
      uploadedImage: null,
      isModalOpen: false,
      isEditModalOpen: false,
      isDeleteModalOpen: false,
      isResetModalOpen: false,
      isSubmitted: false,
      employees: [],
      editIndex: -1,
      formData: {
        fname: '',
        mname: '',
        lname: '',
        email: '',
        dept: '',
        id: '',
        photoURL: '',
        creationDate: '',
        active: false,
        deactivated: false,
      },
      
    };
  },
  computed: {
    fullName() {
      return `${this.formData.fname} ${this.formData.mname} ${this.formData.lname}`;
    },
    formattedDate() {
      return function (creationDate) {
        const options = { month: 'long', day: 'numeric', year: 'numeric' };
        return new Date(creationDate).toLocaleDateString('en-US', options);
      };
    },
    filteredEmployees() {
      return this.employees.filter((employee) => {
        const nameMatches = employee.fname.toLowerCase().includes(this.searchName.toLowerCase()) ||
                            employee.lname.toLowerCase().includes(this.searchName.toLowerCase());
        const deptMatches = employee.dept.toLowerCase().includes(this.searchDept.toLowerCase());
        
        return nameMatches && deptMatches;
      });
    },
  },
  methods: {
    addEmployee() {
      this.formData.creationDate = new Date().toISOString();
      this.isSubmitted = true;
      this.employees.push({
        ...this.formData,
        photoURL: this.uploadedImage,
      });
      this.resetForm();
      this.isModalOpen = false;
    },
    handleImageUpload(event) {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = (e) => {
          this.uploadedImage = e.target.result;
        };
        reader.readAsDataURL(file);
      }
    },
    toggleModal() {
      this.resetForm();
      this.isModalOpen = !this.isModalOpen;
    },
    toggleDeleteModal(index) {
      this.editIndex = index;
      this.isDeleteModalOpen = !this.isDeleteModalOpen;
    },
    deleteEmployee() {
      this.employees.splice(this.editIndex, 1);
      this.isDeleteModalOpen = false;
      this.editIndex = -1;
      this.resetForm();
    },
    toggleEditModal(index) {
      this.editIndex = index;
      this.formData = { ...this.employees[index] };
      this.isEditModalOpen = !this.isEditModalOpen;
    },
    saveEditModalChanges() {
      this.employees[this.editIndex] = { ...this.formData };
      this.isEditModalOpen = false;
      this.resetForm();
    },
    toggleResetModal() {
      this.isResetModalOpen = !this.isResetModalOpen;
    },
    resetForm() {
      this.formData = {
        fname: '',
        mname: '',
        lname: '',
        email: '',
        dept: '',
        id: '',
        creationDate: '',
        active: false,
        deactivated: false,
      };
      this.uploadedImage = null;
      this.isSubmitted = false;
      this.isResetModalOpen = false; // Close the reset modal after resetting the form
    },
    performSearch() {
      // No need to explicitly do anything here since the computed property updates automatically
      // based on changes to searchName and searchDept
    },
  },
};
</script>







<template>

<!--Nav Bar-->
<div class="overflow-hidden border-y-2">

      <div class="float-right px-10 py-10 space-x-10">
      <ul class="flex">
      <li class="mr-4"> <img src="~/assets/frame.svg" class="w-5 h-5"></li>
      <li class="mr-4"> <img src="~/assets/Message.svg" class="w-5 h-5"> </li>
      <li class="mr-4"> <img src="~/assets/ph_user.svg" class="w-5 h-5"> </li>
      </ul>
      </div>

        <div class="font-plus-jakarta-sans float-right text-black text-center no-underline text-base items-start px-10 py-10 space-x-10">
        <NuxtLink to="/" class="hover:border-b-8 hover:border-opacity-50 hover:border-red-500 hover:pb-11">Home</NuxtLink>
        <NuxtLink to="/accounts" class="hover:border-opacity-50 border-b-8 border-red-500 pb-11">Accounts</NuxtLink>
        <NuxtLink to="/sales" class="hover:border-b-8 hover:border-opacity-50 hover:border-red-500 hover:pb-11">Sales</NuxtLink>
        </div>
 
      <span class="pointer-events-none float-left flex items-center py-4"><img src="~/assets/IntelliSeven_Logo.svg" class="w-120 h-20 overflow-hidden"></span>
</div>

<!--Search Bar-->
<div class="flex py-10 px-10">
      <ul class="flex">
        <li class="mr-5">
      <div class="relative flex items-center text-gray-400">
        <input
          type="text"
          placeholder="Search by name ..."
          autocomplete="off"
          aria-label="Search by name ..."
          class="font-plus-jakarta-sans w-64 px-3 py-2 placeholder-gray-300 text-black rounded-md border border-gray-300"
          v-model="searchName"
          @input="performSearch"
           />
        <svg xmlns="http://www.w3.org/2000/svg" class="absolute right-3 w-5 h-5 pointer-events-none" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
              <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />   </svg>
      </div>
        </li>

        <li class="mr-5">
       
          <div class="relative flex items-center text-gray-400">
                  <input
                    type="text"
                    placeholder="department ..."
                    autocomplete="off"
                    aria-label="department ..."
                    class="font-plus-jakarta-sans w-64 pr-3 pl-12 py-2 placeholder-gray-300 text-black rounded-md border border-gray-300"
                    v-model="searchDept"
                    @input="performSearch"
                  />
                  <img src="~/assets/highlighter.svg" class="absolute w-10 h-10 pointer-events-auto">
                  <svg xmlns="http://www.w3.org/2000/svg" class="absolute ml-2.5 w-5 h-5 text-white opacity-60 cursor-pointer" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M3 4a1 1 0 011-1h16a1 1 0 011 1v2.586a1 1 0 01-.293.707l-6.414 6.414a1 1 0 00-.293.707V17l-4 4v-6.586a1 1 0 00-.293-.707L3.293 7.293A1 1 0 013 6.586V4z" />
                  </svg>
          </div>
        </li>
      </ul>
          
    <div class="flex flex-wrap absolute right-20">
          <button @click="toggleModal" class="bg-red-600 hover:bg-red-300 text-white w-30 px-4 py-2 rounded-md ml-4">
            <ul class="flex">
            <li class="mr-4"><svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />  </svg>
            </li> 
            <li class="mr-4">New</li>
            </ul>
          </button>        
        </div>  

  </div>

  <!--Form-->
<div v-if="isModalOpen">
    <form action="">
        <div class="border-t-2 mx-20">
          <table class="my-10">
            <tbody>
              <tr>
                  <td>
                          <label class="flex items-center justify-center cursor-pointer flex-col">
                            <div class="flex flex-col px-10 py-10">
                              <img
                                v-if="uploadedImage"
                                :src="uploadedImage"
                                alt="Uploaded"
                                class="max-w-full h-auto"
                                style="width: 100px; height: auto;"
                                />
                              <img v-if="!uploadedImage" src="~/assets/Upload.svg" class="outline-2 outline-gray-200 outline-dashed border-spacing-96 px-10 py-10" />
                              <label v-if="!uploadedImage" class="inline-block mb-2 mt-2 flex flex-col items-center text-gray-500">720 KB</label>
                              <label v-if="!uploadedImage" class="inline-block cursor-pointer flex flex-col items-center text-blue-500">Upload Image</label>
                              </div>
                              <input type="file" accept="image/*"  @change="handleImageUpload" class="opacity-0" />
                          </label>
                  </td>
                  <td> 
                            <table>
                            <tbody>
                              <tr> 
                                <td>
                                  <ul class="flex">
                                      <li class="mr-5">
                                          <div class="flex flex-col">
                                            <label class="text-slate-700 text-sm mb-1 ml-2">First Name <span v-show="!formData.fname" class="text-red-500">*</span></label>
                                              <input
                                                type="text"
                                                v-model="formData.fname"
                                                placeholder="Placeholder..."
                                                class="w-64 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm"
                                                />
                                          </div>
                                      </li>
                                        <li class="mr-5">
                                            <div class="flex flex-col">
                                              <label class="text-slate-700 text-sm mb-1 ml-2">Middle Name<span v-show="!formData.mname" class="text-red-500">*</span></label>
                                                <input
                                                  type="text"
                                                  v-model="formData.mname"
                                                  placeholder="Placeholder..."
                                                  class="w-64 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm"
                                                  />
                                            </div>
                                        </li>
                                        <li class="mr-5">
                                            <div class="flex flex-col">
                                              <label class="text-slate-700 text-sm mb-1 ml-2">Last Name<span v-show="!formData.lname" class="text-red-500">*</span></label>
                                                <input
                                                  type="text"
                                                  v-model="formData.lname"
                                                  placeholder="Placeholder..."
                                                  class="w-64 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm"
                                                  />
                                            </div>  
                                        </li>
                                  </ul>
                                </td>
                              </tr>
                              <tr>
                                <td>
                                  <ul class="flex mt-5">
                                    <li class="mr-5">
                                    <div class="flex flex-col">
                                    <label class="text-slate-700 text-sm mb-1 ml-2">Email<span v-show="!formData.email" class="text-red-500">*</span></label>
                                    <input
                                    type="text"
                                    v-model="formData.email"
                                    placeholder="Placeholder..."
                                    class="w-64 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm"
                                      />
                                    </div>
                                    </li>
                                      <li class="mr-5">
                                        <div class="flex flex-col">
                                        <label class="text-slate-700 text-sm mb-1 ml-2">Department<span v-show="!formData.dept" class="text-red-500">*</span></label>
                                          <input
                                            type="text"
                                            v-model="formData.dept"
                                            placeholder="Placeholder..."
                                            class="w-64 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm"
                                            />
                                        </div>
                                      </li>
                                      <li class="mr-5">
                                        <div class="flex flex-col">
                                        <label class="ttext-slate-700 text-sm mb-1 ml-2">ID No.<span v-show="!formData.id" class="text-red-500">*</span></label>
                                          <input
                                            type="text"
                                            v-model="formData.id"
                                            placeholder="Placeholder..."
                                            class="w-64 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm"
                                            />
                                        </div>
                                      </li>
                                    </ul>
                                  </td>
                              </tr>
                              <tr>
                                <td>
                                  <ul class="flex">
                                      <div class="mt-5">
                                          <li class='mr-10'>
                                            <label class="flex items-center">
                                            <input
                                              type="checkbox"
                                              v-model="formData.active"
                                              class="mr-2 h-4 w-4"
                                            />
                                            <span class="text-slate-600 text-sm font-medium"> Active</span>
                                            </label>
                                          </li>
                                      </div>

                                      <div class="mt-5">
                                        <li class='mr-10'>
                                          <label class="flex items-center">
                                          <input
                                            type="checkbox"
                                            v-model="formData.deactivated"
                                            class="mr-2 h-4 w-4 bg-gray-500"
                                          />
                                          <span class="text-slate-600 text-sm font-medium"> Deactive</span>
                                        </label>
                                        </li>
                                      </div>
                                      </ul>
                                </td>
                              </tr>
                            </tbody>
                            </table>
                  </td>
                  <td>
                            <div class="-mt-10"> 
                            <ul class="flex mt-5"><li class="mr-5"> <button type="submit" @click="addEmployee" class="bg-green-600 hover:bg-green-300 text-white w-30 px-5 py-5 rounded-md w-32 ml-4">Add</button></li></ul>
                            <input type="hidden" name="creationDate" v-model="formData.creationDate">
                            <ul class="flex mt-5"><li class="mr-5"> <button class="bg-gray-300 hover:bg-gray-100 text-white w-30 px-5 py-5 rounded-md w-32 ml-4" @click="toggleModal">Cancel</button></li></ul>
                            </div>
                  </td> 
               </tr>
            </tbody>
          </table>
        </div>
    </form>
</div>

<!--Display Table-->
  <div class="relative">
   <fieldset class="border-t-4 border-red-500 border-opacity-30">
        <legend class="text-black font-medium pl-10 pr-5 font-plus-jakarta-sans">Employees Account</legend>
    </fieldset>
  </div>
  <div class = "rounded mx-4 mt-8 p-2">
            <table class = "min-w-full">
                <thead>
                    <tr>
                      <th><input
                          type="checkbox"
                          v-model="accounts"
                          class="mr-2 h-4 w-4 bg-gray-500"
                          /></th>
                        <th class = "py-2 px-3 text-left text-slate-500 text-sm font-medium"> File Name / Id No. </th>
                        <th class = "py-2 px-3 text-left text-slate-500 text-sm font-medium"> Date </th>
                        <th class = "py-2 px-3 text-left text-slate-500 text-sm font-medium"> Email </th>
                        <th class = "py-2 px-3 text-left text-slate-500 text-sm font-medium"> Department </th>
                        <th class = "py-2 px-3 text-left text-slate-500 text-sm font-medium"> Active </th>
                    </tr>
                </thead>
                <tbody>
                  <tr v-for="(employee, index) in employees" :key="index" class="even:bg-gray-100 odd:bg-white-100">
                  <td><input
                    type="checkbox"
                    v-model="accounts"
                    class="h-4 w-4 bg-gray-500"
                  /></td>
                  <td class="py-2 px-1 flex items-center">
                    <img v-if="employee.photoURL" :src="employee.photoURL" alt="Employee Photo" class="w-10 h-10 mr-2" />
                    <div>
                      <div class="text-[#101828]">{{ employee.fname }} {{ employee.mname }} {{ employee.lname }}</div>
                      <div class="text-[#667085]">{{ employee.id }}</div>
                    </div>
                  </td>
                  <td class="text-[#667085] py-2 px-3">{{ formattedDate(employee.creationDate) }}</td>
                  <td class="text-[#667085] py-2 px-3">{{ employee.email }}</td>
                  <td class="text-[#667085] py-2 px-3">{{ employee.dept }}</td>
                  <td class="text-[#667085] py-2 px-3" :style="{'color': employee.active ? 'green' : (employee.deactivated ? 'red' : '')}">
                    {{ employee.active ? 'Active' : (employee.deactivated ? 'Deactivated' : '') }}
                  </td>
                  <td>
                    <button @click="toggleEditModal(index)">
                      <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M12 5v.01M12 12v.01M12 19v.01M12 6a1 1 0 110-2 1 1 0 010 2zm0 7a1 1 0 110-2 1 1 0 010 2zm0 7a1 1 0 110-2 1 1 0 010 2z" />
                        </svg>
                    </button>
   
                      </td>
                      <td v-if="isEditModalOpen" class="bg-gray-100 flex justify-center items-center">
                        <form action="" @submit.prevent > 
                        <table>
                        <div class="">
                          <tr>
                            <td>
                              <ul class="flex float-right my-5">
                                <li class="mx-6">
                                  <button @click="toggleResetModal()">
                                  
                                  <img src="~/assets/reset.svg" class="absolute w-10 h-10 pointer-events-auto">
                                </button>
                                </li> 
                                <li class="mx-6">
                                  <button @click="saveEditModalChanges">
                                  <img src="~/assets/edit.svg" class="absolute w-10 h-10 pointer-events-auto">
                                </button>
                                </li>
                                <li class="mx-6">
                                  <button @click="toggleDeleteModal(index)">
                                  <img src="~/assets/delete.svg" class="absolute w-10 h-10 pointer-events-auto">
                                </button>
                                </li>
                              </ul>
                            </td>
                          </tr>
                          <tr>
                            <td>
                          <label class="flex items-center justify-center cursor-pointer flex-col">
                                <div class="float-center">
                                  <ul class="flex">
                                  <li class="mr-4 my-4"> 
                                    <img
                                      v-if="formData.photoURL"
                                      :src="formData.photoURL"
                                      alt="Employee Photo"
                                      class="max-w-full h-auto"
                                      style="width: 150px; height: auto;"
                                    />
                                    <img
                                      v-else
                                      src="~/assets/Upload.svg"
                                      class="outline-2 outline-gray-200 outline-dashed border-spacing-96 px-10 py-10"
                                      style="width: 150px; height: 150px;"
                                    />
                                  </li>
      
                                  
                                <li class="mr-4 my-4">
                                  <label class="inline-block cursor-pointer items-center text-blue-500">Upload Image</label>
                                  <input type="file" accept="image/*" @change="handleEditImageUpload" class="opacity-0" />
                                  <div class="text-[#101828]">{{ employee.fname }} {{ employee.mname }} {{ employee.lname }}</div>
                                  <div class="text-[#667085]">{{ employee.id }}</div>
                                </li>
                              </ul>
                                </div>
                            
                          </label>
                          <ul class="flex">
                          <li class="mr-4 my-4">
                            <div class="flex flex-col">
                            <label class="ttext-slate-700 text-sm mb-1 ml-2">First Name<span v-show="!formData.fname" class="text-red-500">*</span></label>
                          <input v-model="formData.fname" class="w-30 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm" />
                        </div>
                        </li>
                          <li class="mr-4 my-4">
                            <div class="flex flex-col">
                            <label class="ttext-slate-700 text-sm mb-1 ml-2">Email<span v-show="!formData.fname" class="text-red-500">*</span></label>
                            <input v-model="formData.email" class="w-30 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm" />
                          </div>
                          </li>
                        </ul>
                      </td>
                      </tr>
                      <tr>
                      <td>
                        <ul class="flex">
                          <li class="mr-4 my-4">
                            <div class="flex flex-col">
                            <label class="ttext-slate-700 text-sm mb-1 ml-2">Middle Name<span v-show="!formData.fname" class="text-red-500">*</span></label>
                            <input v-model="formData.mname" class="w-30 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm"/>
                          </div>
                          </li>
                          <li class="mr-4 my-4">
                            <div class="flex flex-col">
                            <label class="ttext-slate-700 text-sm mb-1 ml-2">Department<span v-show="!formData.fname" class="text-red-500">*</span></label>
                            <input v-model="formData.dept" class="w-30 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm" />
                          </div>
                          </li>
                          </ul>
                     </td>
                     </tr>
                     <tr>
                     <td>
                      <ul class="flex">
                          <li class="mr-4 my-4">
                            <div class="flex flex-col">
                            <label class="ttext-slate-700 text-sm mb-1 ml-2">Last Name<span v-show="!formData.fname" class="text-red-500">*</span></label>
                            <input v-model="formData.lname" class="w-30 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm"/>
                          </div>
                          </li>
                          <li class="mr-4 my-4">
                            <div class="flex flex-col">
                            <label class="ttext-slate-700 text-sm mb-1 ml-2">ID No.<span v-show="!formData.fname" class="text-red-500">*</span></label>
                            <input v-model="formData.id" class="w-30 px-3 py-3 placeholder-transparent border border-gray-200 rounded-md peer focus:outline-none focus:border-gray-500 focus:shadow-sm" />
                            </div>
                          </li>
                          </ul>
                     </td>
                     </tr>
                     <tr>
                     <td>
                       
                      <input type="checkbox" v-model="formData.active" /> <label class="text-gray-500 font-bold mr-5 ml-2">Active</label>
                          <input type="checkbox" v-model="formData.deactivated" /> <label class="text-red-500 font-bold mr-5 ml-2">Deactived</label>
                     </td>
                              </tr>
                              <tr>
                                <td>
                                  <button @click="toggleEditModal" class="my-4 bg-gray-200 hover:bg-green-300 text-white px-8 py-5 rounded-md">
                                  Cancel
                                </button>
                                  <button @click="saveEditModalChanges" class="my-4 ml-4 bg-green-600 hover:bg-green-300 text-white px-8 py-5 rounded-md">
                                  Update
                                </button>
                                </td>
                              </tr>
                              </div>
                        </table>
                            
                      </form> 
                      </td>
                      
                    </tr>
                </tbody>
            </table>
  </div>

<!-- Delete Modal -->
<div v-if="isDeleteModalOpen" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50">
  <div class="bg-white p-4 rounded-md pt-20 border-t-8 border-red-500 w-96 h-30 flex flex-col justify-center">
    <ul class="flex">
      <li class="mr-4">
    <img src="~/assets/Trash.svg" class="mr-20 w-30 h-30 mb-2">
  </li>
  <li class="mr-4">
    <h1 class="font-bold mb-2">Delete Account</h1> 
    <p class="text-left mb-4">You'll permanently delete all the data, messages, photos and important information.</p>
  </li>
    </ul>
    <div class="flex justify-center mb-5    mt-20">
   
      <button @click="toggleDeleteModal" class="px-8 py-4 bg-gray-300 rounded-md text-white mr-2">Cancel</button>
      <button @click="deleteEmployee" class="px-8 py-4 bg-red-600 rounded-md text-white ml-2">Delete</button>
    </div>
  </div>
</div>


<!-- Reset Modal -->
<div v-if="isResetModalOpen" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50">
  <div class="bg-white p-4 rounded-md pt-20 border-t-8 border-yellow-500 w-96 h-30 flex flex-col justify-center">
    <ul class="flex">
      <li class="mr-4">
    <img src="~/assets/Reboot.svg" class="mr-20 w-30 h-30 mb-2">
  </li>
  <li class="mr-4">
    <h1 class="font-bold mb-2">Reset Profile</h1> 
    <p class="text-left mb-4">Are you sure you want to reset this profile? If you reset this profile, you will permanently lose all the important data.</p>
  </li>
    </ul>
    <div class="flex justify-center mb-5    mt-20">
   
      <button @click="toggleResetModal()" class="px-8 py-4 bg-gray-300 rounded-md text-white mr-2">Cancel</button>
      <button @click="resetForm" class="px-8 py-4 bg-yellow-500 rounded-md text-white ml-2">Reset</button>
    </div>
  </div>
</div>


</template>



<style>

</style>