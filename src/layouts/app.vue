<template>
  <SidebarProvider
    :style="{
      '--sidebar-width': '350px',
    }"
    class="no-scrollbar flex grow flex-col overflow-auto overscroll-none [--footer-height:calc(theme(spacing.5))] [--header-height:calc(theme(spacing.14))]"
  >
    <Titlebar />
    <div
      class="no-scrollbar flex grow overflow-hidden overscroll-none border-y"
    >
      <LeftSidebar
        class="top-[calc(var(--header-height)_+_1px)] bottom-[calc(var(--footer-height)_+_1px)] h-[calc(100svh_-_var(--header-height)_-_var(--footer-height)))]"
      />
      <div
        class="no-scrollbar bg-background flex grow flex-col overflow-auto overscroll-none"
      >
        <Toolbar />
        <SubNavigation />
        <main
          class="no-scrollbar bg-background m-2 flex grow flex-col overflow-auto overscroll-none rounded-md border shadow-xl"
        >
          <RouterView v-slot="{ Component, route }">
            <template v-if="Component">
              <Transition
                enter-active-class="transition ease-in-out duration-200"
                enter-from-class="opacity-0"
                enter-to-class="opacity-100"
                leave-active-class="transition ease-in-out duration-200"
                leave-from-class="opacity-100"
                leave-to-class="opacity-0"
                mode="out-in"
              >
                <Component :is="Component" :key="route.path" />
              </Transition>
            </template>
          </RouterView>
        </main>
      </div>
      <RightSidebar
        side="right"
        class="top-[calc(var(--header-height)_+_1px)] bottom-[calc(var(--footer-height)_+_1px)] h-[calc(100svh_-_var(--header-height)_-_var(--footer-height)))]"
      />
    </div>
    <Footbar />
  </SidebarProvider>
</template>
