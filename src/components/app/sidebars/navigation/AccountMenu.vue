<script setup lang="ts">
import emitter from "@/modules/mitt"
import { useCurrentUser } from "vuefire"

const user = useCurrentUser()
</script>

<template>
  <SidebarMenu>
    <SidebarMenuItem id="tour-account-menu">
      <TooltipProvider>
        <Tooltip>
          <DropdownMenu>
            <TooltipTrigger as-child>
              <DropdownMenuTrigger as-child>
                <SidebarMenuButton class="p-0">
                  <Avatar class="rounded-md">
                    <AvatarImage
                      :src="user?.photoURL!"
                      :alt="user?.displayName"
                      referrerpolicy="no-referrer"
                    />
                    <AvatarFallback class="rounded-md"> CN </AvatarFallback>
                  </Avatar>
                  <div class="grid flex-1 text-left text-sm leading-tight">
                    <span class="truncate font-semibold">{{
                      user?.displayName
                    }}</span>
                    <span class="truncate text-xs">{{ user?.email }}</span>
                  </div>
                </SidebarMenuButton>
              </DropdownMenuTrigger>
            </TooltipTrigger>
            <TooltipContent side="right"> Account </TooltipContent>
            <DropdownMenuContent
              class="w-56"
              align="end"
              side="right"
              :side-offset="4"
            >
              <DropdownMenuLabel class="p-0 font-normal">
                <div
                  class="flex items-center gap-2 px-1 py-1.5 text-left text-sm"
                >
                  <Avatar class="size-8 rounded-lg">
                    <AvatarImage
                      :src="user?.photoURL!"
                      :alt="user?.displayName"
                      referrerpolicy="no-referrer"
                    />
                    <AvatarFallback class="rounded-lg"> CN </AvatarFallback>
                  </Avatar>
                  <div class="grid flex-1 text-left text-sm leading-tight">
                    <span class="truncate font-semibold">{{
                      user?.displayName
                    }}</span>
                    <span class="text-muted-foreground truncate text-xs">
                      {{ user?.email }}
                    </span>
                  </div>
                </div>
              </DropdownMenuLabel>
              <DropdownMenuSeparator />
              <DropdownMenuGroup>
                <DropdownMenuItem
                  @click="emitter.emit('Dialog.Settings.Open', 'preferences')"
                >
                  <icon-lucide-settings />
                  Settings
                  <DropdownMenuShortcut>⌘ ,</DropdownMenuShortcut>
                </DropdownMenuItem>
                <DropdownMenuItem
                  @click="emitter.emit('Dialog.Settings.Open', 'account')"
                >
                  <icon-lucide-circle-user />
                  Account
                </DropdownMenuItem>
                <DropdownMenuItem
                  @click="emitter.emit('Dialog.Settings.Open', 'billing')"
                >
                  <icon-lucide-credit-card />
                  Billing
                </DropdownMenuItem>
              </DropdownMenuGroup>
              <DropdownMenuSeparator />
              <DropdownMenuGroup>
                <DropdownMenuItem @click="emitter.emit('Dialog.Exit.Open')">
                  <icon-lucide-log-out />
                  Log out
                </DropdownMenuItem>
              </DropdownMenuGroup>
            </DropdownMenuContent>
          </DropdownMenu>
        </Tooltip>
      </TooltipProvider>
    </SidebarMenuItem>
  </SidebarMenu>
</template>
