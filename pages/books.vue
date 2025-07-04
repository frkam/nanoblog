<template>
  <div class="flex flex-col gap-8">
    <div
      class="flex gap-2 prose"
      v-for="{
        id,
        title,
        description,
        author,
        date,
        url,
        cover,
      } in booksForCurrentLocale"
      :key="id"
    >
      <a :href="url" class="w-18 shrink-0 flex items-center">
        <NuxtImg :src="cover" />
      </a>

      <div class="flex flex-col">
        <a :href="url">{{ title }}</a>
        {{ description }}

        <div class="flex gap-2 text-xs opacity-50">
          {{ getBookLocalizedDate(date) }},
          {{ author }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import type { BooksCollectionItem } from "@nuxt/content";

const { locale } = useI18n();

const { data: books } = await useAsyncData<BooksCollectionItem[]>(
  "books",
  async () => {
    return queryCollection("books").all();
  }
);

const booksForCurrentLocale = computed(() => {
  console.log(books.value);

  if (!books.value) {
    return [];
  }

  return books.value.filter((book: BooksCollectionItem) => {
    return (
      book.path.startsWith(`/books`) && book.path.includes(`/${locale.value}`)
    );
  });
});

const getBookLocalizedDate = (bookDate: string) => {
  const [day, month, year] = bookDate.split("-").map(Number);
  const date = new Date(year, month - 1, day);

  return date.toLocaleDateString(locale.value);
};
</script>
