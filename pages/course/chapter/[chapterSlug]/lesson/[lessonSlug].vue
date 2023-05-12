<template>
    <div>
        <p class="mt-0 mb-1 font-bold uppercase text-slate-400">
            Урок {{ chapter.number }} - {{ lesson.number }}
        </p>

        <h2 class="my-0">{{ lesson.title }}</h2>

        <div class="flex mt-2 mb-8 space-x-4">
            <NuxtLink
                v-if="lesson.sourceUrl"
                class="font-normal text-gray-500 text-md"
                :to="lesson.sourceUrl"
                target="_blank"
            >
                Скачать исходный код
            </NuxtLink>

            <NuxtLink
                v-if="lesson.downloadUrl"
                class="font-normal text-gray-500 text-md"
                :to="lesson.downloadUrl"
                target="_blank"
            >
                Скачать видео
            </NuxtLink>
        </div>

        <VideoPlayer v-if="lesson.videoId" :video-id="lesson.videoId" />

        <p>{{ lesson.text }}</p>

        <LessonCompleteButton
            :model-value="isLessonComplete"
            @update:model-value="toggleComplete"
        />
    </div>
</template>

<script setup>
const course = useCourse();
const route = useRoute();

definePageMeta({
    middleware: [function(to, from) {
        },
        'auth',
    ],
});

const chapter = computed(() => {
    return course.chapters.find(
        (chapter) => chapter.slug === route.params.chapterSlug
    );
});

const lesson = computed(() => {
    return chapter.value.lessons.find(
        (lesson) => lesson.slug === route.params.lessonSlug
    );
});

const title = computed(() => {
    return `${lesson.value.title} - ${course.title}`;
});

useHead({
    title,
});

const progress = useLocalStorage("progress", []);

const isLessonComplete = computed(() => {
    if (!progress.value[chapter.value.number - 1]) {
        return false;
    }

    if (!progress.value[chapter.value.number - 1][lesson.value.number - 1]) {
        return false;
    }

    return progress.value[chapter.value.number - 1][lesson.value.number - 1];
});

const toggleComplete = () => {
    if (!progress.value[chapter.value.number - 1]) {
        progress.value[chapter.value.number - 1] = [];
    }

    progress.value[chapter.value.number - 1][lesson.value.number - 1] =
        !isLessonComplete.value;
};
</script>
