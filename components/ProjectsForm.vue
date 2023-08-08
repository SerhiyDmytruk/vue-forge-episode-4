<script setup lang="ts">
import { hardhat } from 'viem/dist/types/chains';


const form = reactive({
  title: "",
  description: "",
  image: "",
  categoryUuid: "",
  softCap: 10_000,
  hardCap: 25_000,
  startAt: useDateFormat(new Date(), "YYY-MM-DD").value,
  finishedAt: useDateFormat(getDateXMonthsFromNow(6), "YYYY-MM-DD").value
});

watch([() => form.softCap], ([soft]) => {
  if(soft > form.hardCap) {
    const plus500 = soft + 5000;
    form.hardCap  =plus500 < 10000 ? plus500: soft;
  }
});

watch([() => form.hardCap], ([hard]) => {
  if(form.softCap > hard){
    const minus500 = hard - 5000;
    form.softCap = minus500 < 0 ? hard : minus500;
  }
})


// Get categories for dropdown
const { list: categories, fetchAll } = useCategories();
fetchAll();

const category = computed(() => {
  return categories.value.find(
    (category) => category.uuid === form.categoryUuid
  )
});

// handle form submit
const submitForm = async ()=> {
  useAlerts().success("Project created");
}

</script>

<template>
  <div class="w-full max-w-5xl mx-auto mb-20">
    <h3 class="py-5 text-3xl">Kickstart your own project</h3>
    <div class="grid grid-cols-12 gap-8">
      <form @submit.prevent="submitForm" class="w-full col-span-8">
        <FormField
          label="What is your projects name?"
          name="title"
          v-model="form.title"
          hint="Use a very handy title that people could identify your
                project"
        />

        <FormField
          label="What is your projects name?"
          name="description"
          v-model="form.description"
          as="textarea"
          hint="Describe with full detail your project so that people understand exactly what it is about."
        />

        <AppFileUpload
          label="Upload a cover image for your project"
          bucket="projects"
          @file:uploaded="form.image = $event"
          class="mb-4"
        />

        <FormField
          label="Which category does your project fit in?"
          name="categoryUuid"
          v-model="form.categoryUuid"
          as="select"
          hint="Selecting a fitting category ensures the right people find your project."
        >
          <option :value="null" disabled selected>Pick one</option>
          <option 
            v-for="category in categories"
            :key="category.uuid"
            :value="category.uuid">
            {{ category.name }}
          </option>
        </FormField>

        <FormField
            label="What is the soft cap of your projects?"
            name="softCap"
            type="range"
            min="0"
            max="100000"
            class="range"
            step="5000"
            v-model.number="form.softCap"
            hint="Soft cap is the minimum amount of money that you need to raise in order to start your project.">

            <template #label-text-alt>
                <Money :amount="form.softCap" />
            </template>

            <template #after-input>
              <div class="flex justify-between w-full px-2 text-xs">
                <span>|</span>
                <span>|</span>
                <span>|</span>
                <span>|</span>
                <span>|</span>
              </div>
              <div class="flex justify-between w-full px-2 text-xs">
                <span> <Money :amount="10000"/> </span>
                <span> <Money :amount="25000"/> </span>
                <span> <Money :amount="50000"/> </span>
                <span> <Money :amount="75000"/> </span>
                <span> <Money :amount="100000"/> </span>
              </div>
            </template>

        </FormField>

      </form>
    </div>
  </div>
</template>
