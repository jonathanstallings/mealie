<template>
  <div>
    <client-only>
      <RecipePage v-if="recipe" :recipe="recipe" />
    </client-only>
  </div>
</template>

<script lang="ts">
import { defineComponent, useAsync, useMeta, useRoute, useRouter } from "@nuxtjs/composition-api";
import RecipePage from "~/components/Domain/Recipe/RecipePage/RecipePage.vue";
import { usePublicApi } from "~/composables/api/api-client";

export default defineComponent({
  components: { RecipePage },
  layout: "basic",
  setup() {
    const route = useRoute();
    const router = useRouter();
    const recipeId = route.value.params.id;
    const api = usePublicApi();

    const { title } = useMeta();

    const recipe = useAsync(async () => {
      const { data, error } = await api.shared.getShared(recipeId);

      if (error) {
        console.error("error loading recipe -> ", error);
        router.push("/");
      }

      if (data) {
        title.value = data?.name || "";
      }

      return data;
    });

    return {
      recipe,
    };
  },
  head: {},
});
</script>
