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
      <form action="">
        <FormField
          label="What is your projects name?"
          name="title"
          v-model="form.title"
          hint="Use a very handy title that people could identify your
                project"
        />
      </form>
    </div>
  </div>
</template>
