<template>
<div>
  <div class="min-h-full flex flex-col justify-center py-12 sm:px-6 lg:px-8">
    <div class="sm:mx-auto sm:w-full sm:max-w-md">
      <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">Upload images for collage</h2>
    </div>

    <div class="mt-8 sm:mx-auto sm:w-full sm:max-w-md">
      <div class="bg-white py-8 px-4 shadow sm:rounded-lg sm:px-10">
        <form class="space-y-6" @submit.prevent="upload">
          <div>
            <label for="file" class="block text-sm font-medium text-gray-700"> Images </label>
            <div class="mt-1">
              <input @change="handleImagesChanged" id="file" name="file" type="file" accept="image/*" required class="appearance-none block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" multiple />
            </div>
          </div>

          <div>
            <div v-if="uploading" class="text-center">Please wait, upload in progress</div>
            <button v-else type="submit" class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
              Upload & Generate Collage
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>

  <cld-image
    v-if="!uploading && cloudinaryImages.length"
    :public-id="template"
    width="600"
    height="680"
    class="mx-auto my-10"
  >
    <cld-transformation
      v-for="(coordinate,index) in templateCoordinates"
      :key="index"
      :overlay="getRandomImageUrl()"
      width="574"
      :y="coordinate.y"
      :x="coordinate.x"
    />
  </cld-image>
</div>
</template>

<script>
export default {
  data(){
    return {
      images:null,
      uploading:false,
      cloudinaryImages:[],
      template:'nuxtjs-image-collage/templates/Pink_Handdrawn_Heart_Polaroid_Photo_Collage',
      templateCoordinates: [
        {x:654, y:-20, label:'Center left'},
        {x:-654, y:-20,description:'Center right'},
        {x:0, y:510,description:'Bottom center'},
        {x:0, y:-535,description:'Top center'},
        {x:654, y:-535,description:'Top right'},
        {x:-654, y:510,description:'Bottom left'},
        {x:654, y:510,description:'Bottom right'},
        {x:-654, y:-535,description:'Top left'},
      ],
    }
  },
  methods:{
    handleImagesChanged(e){
     this.images = e.target.files;
    },
    readData : (f) =>
        new Promise((resolve) => {
          const reader = new FileReader();
          reader.onloadend = () => resolve(reader.result);
          reader.readAsDataURL(f);
    }),
    async upload(){
      this.uploading = true;
      for(let image of this.images){
        const data = await this.readData(image);
        const instance = await this.$cloudinary.upload(data, {
          folder: "nuxtjs-image-collage",
          uploadPreset: "default-preset",
        });
        this.cloudinaryImages.push(instance);
      }
      this.uploading = false;
    },
    getRandomImageUrl(){
      const randomIndex = Math.floor(Math.random() * this.cloudinaryImages.length);
      const randomImage = this.cloudinaryImages[randomIndex];
      return randomImage.public_id.replace(new RegExp( '/','g'), ':');
    },
  }
}
</script>
