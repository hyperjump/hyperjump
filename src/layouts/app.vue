<template>
  <SidebarProvider
    class="no-scrollbar flex grow flex-col overflow-auto overscroll-none [--footer-height:--spacing(5)] [--header-height:--spacing(13)]"
  >
    <Titlebar />
    <Separator />
    <div class="no-scrollbar flex grow overflow-hidden overscroll-none">
      <LeftSidebar
        class="top-[calc(var(--header-height)+1px)] bottom-[calc(var(--footer-height)+1px)] h-[calc(100svh-var(--header-height)-var(--footer-height)-2px))]"
      />
      <div
        class="no-scrollbar flex grow divide-x divide-dashed overflow-hidden overscroll-none"
      >
        <FlowSidebar />
        <div
          class="no-scrollbar flex grow flex-col overflow-auto overscroll-none"
        >
          <Tabbar />
          <SubNavigation />
          <main
            class="no-scrollbar bg-background flex grow flex-col overflow-auto overscroll-none border-t"
          >
            <RouterView v-slot="{ Component, route }">
              <template v-if="Component">
                <Transition
                  enter-active-class="transition ease-in-out "
                  enter-from-class="opacity-0"
                  enter-to-class="opacity-100"
                  leave-active-class="transition ease-in-out "
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
        <RightSidebar />
      </div>
    </div>
    <Separator />
    <Footbar />
  </SidebarProvider>
</template>
