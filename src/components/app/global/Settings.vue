<script setup lang="ts">
import { accents, defaultAccent, languages, themes } from "@/helpers/defaults"
import { getInitials } from "@/helpers/utilities"
import emitter from "@/modules/mitt"
import { store } from "@/modules/theme"
import {
  deleteUser,
  sendEmailVerification,
  unlink,
  verifyBeforeUpdateEmail,
} from "firebase/auth"
import { ref as storageRef } from "firebase/storage"
import { OverlayScrollbarsComponent } from "overlayscrollbars-vue"
import { toast } from "vue-sonner"
import {
  updateCurrentUserProfile,
  useCurrentUser,
  useFirebaseStorage,
  useStorageFile,
} from "vuefire"
import IconCircleUserRound from "~icons/lucide/circle-user-round"
import IconPaintbrush from "~icons/lucide/paintbrush"
import IconSettings from "~icons/lucide/settings"

const openSettings = ref(false)
const activeTab = ref("general")

emitter.on("Dialog.Settings.Open", (event: unknown) => {
  const selected = event as string
  activeTab.value = selected
  openSettings.value = !openSettings.value
})

const user = useCurrentUser()

const displayName = computed({
  get: () => user.value?.displayName ?? "User",
  set: (value: string) => {
    updateCurrentUserProfile({ displayName: value })
  },
})

const photoURL = computed({
  get: () => user.value?.photoURL ?? "",
  set: (value: string) => {
    updateCurrentUserProfile({ photoURL: value })
  },
})

const sendingVerificationEmail = ref(false)
const sendVerificationEmail = async () => {
  sendingVerificationEmail.value = true

  await sendEmailVerification(user.value!)
    .then(() => {
      toast.info("Verification email sent", {
        description: "Please check your inbox for the verification email.",
      })
    })
    .catch((error) => {
      toast.error("Failed to send verification email", {
        description: error.message,
      })
    })
    .finally(() => {
      sendingVerificationEmail.value = false
    })
}

const newEmail = ref("")
const changingEmail = ref(false)
const changeEmail = async () => {
  changingEmail.value = true

  await verifyBeforeUpdateEmail(user.value!, newEmail.value)
    .then(() => {
      toast.info("Verification email sent", {
        description: "Please check your inbox for the verification email.",
      })
    })
    .catch((error) => {
      toast.error("Failed to send verification email", {
        description: error.message,
      })
    })
    .finally(() => {
      changingEmail.value = false
    })
}

const deletingAccount = ref(false)
const deleteAccount = async () => {
  deletingAccount.value = true

  await deleteUser(user.value!)
    .then(() => {
      toast.success("Account deleted", {
        description: "Your account has been successfully deleted.",
      })
    })
    .catch((error) => {
      toast.error("Failed to delete account", {
        description: error.message,
      })
    })
    .finally(() => {
      deletingAccount.value = false
    })
}

const unlinkingProviderMap = ref<Record<string, boolean>>({})
const unlinkProvider = async (providerId: string) => {
  unlinkingProviderMap.value = {
    ...unlinkingProviderMap.value,
    [providerId]: true,
  }

  await unlink(user.value!, providerId)
    .then(() => {
      toast.success("Provider unlinked", {
        description: "The provider has been successfully unlinked.",
      })
    })
    .catch((error) => {
      toast.error("Failed to unlink provider", {
        description: error.message,
      })
    })
    .finally(() => {
      unlinkingProviderMap.value = {
        ...unlinkingProviderMap.value,
        [providerId]: false,
      }
    })
}

const storage = useFirebaseStorage()
const profilePhotoFileRef = storageRef(
  storage,
  `${user.value?.uid}/images/profilePhoto.jpg`
)

const { url, uploadProgress, uploadError, uploadTask, upload } =
  useStorageFile(profilePhotoFileRef)

watch(
  () => url.value,
  (newVal, oldVal) => {
    if (oldVal === undefined) return
    if (oldVal === null && newVal) {
      try {
        photoURL.value = newVal
        toast.success("Profile picture updated", {
          description: "Your profile picture has been updated successfully.",
        })
        reset()
        filename.value = ""
      } catch (error) {
        console.error("Error updating profile with new photo URL:", error)
        toast.error("Failed to update profile picture", {
          description: error as string,
        })
      }
    }
  }
)

const uploadPicture = async () => {
  const data = files.value?.item(0)
  if (data) {
    try {
      toast.info("Uploading profile picture", {
        description: "Please wait while we upload your profile picture.",
      })
      filename.value = data.name
      await upload(data)
    } catch (error) {
      console.error("Error uploading picture or updating profile:", error)
      toast.error("Failed to upload profile picture", {
        description: error as string,
      })
    }
  }
}

const filename = ref<string>()
const { files, open, reset } = useFileDialog()

watch(files, uploadPicture)

const { locale } = useI18n()
watch(locale, (newLocale) => localStorage.setItem("locale", newLocale))

const getComputedProviderName = (provider: string) => {
  switch (provider) {
    case "google.com":
      return "Google"
    case "password":
      return "Primary email"
    default:
      return "Unknown"
  }
}

const accent = useStorage("accent", defaultAccent)

const data = {
  nav: [
    { name: "General", icon: IconSettings, id: "general" },
    { name: "Account", icon: IconCircleUserRound, id: "account" },
    { name: "Appearance", icon: IconPaintbrush, id: "appearance" },
  ],
}
</script>

<template>
  <Dialog v-model:open="openSettings">
    <DialogContent class="!max-h-4/5 !max-w-4/5 overflow-hidden p-0">
      <DialogHeader class="sr-only">
        <DialogTitle>Settings</DialogTitle>
        <DialogDescription>
          Manage your preferences and settings.
        </DialogDescription>
      </DialogHeader>
      <Tabs :default-value="activeTab" orientation="vertical">
        <SidebarProvider>
          <Sidebar collapsible="none" class="bg-muted/10">
            <TabsList class="contents">
              <SidebarContent>
                <SidebarGroup>
                  <SidebarGroupContent>
                    <SidebarMenu>
                      <SidebarMenuItem
                        v-for="item in data.nav"
                        :key="item.name"
                      >
                        <TabsTrigger
                          :value="item.id"
                          as-child
                          class="data-[state=active]:bg-sidebar-accent text-secondary-foreground data-[state=active]:text-sidebar-accent-foreground data-[state=active]:shadow-none"
                        >
                          <SidebarMenuButton
                            :is-active="item.id === activeTab"
                            class="justify-start"
                          >
                            <component :is="item.icon" />
                            <span>{{ item.name }}</span>
                          </SidebarMenuButton>
                        </TabsTrigger>
                      </SidebarMenuItem>
                    </SidebarMenu>
                  </SidebarGroupContent>
                </SidebarGroup>
              </SidebarContent>
            </TabsList>
          </Sidebar>
          <main class="flex flex-1 flex-col overflow-hidden">
            <OverlayScrollbarsComponent
              defer
              :options="{ scrollbars: { autoHide: 'scroll' } }"
            >
              <TabsContent value="account">
                <div class="flex h-full w-full flex-col gap-6 px-8 py-6">
                  <div class="flex flex-col">
                    <h3 class="text-lg font-semibold">Account</h3>
                    <p class="text-muted-foreground flex items-center gap-2">
                      Manage your account settings.
                    </p>
                  </div>
                  <Separator />
                  <div class="flex items-center gap-4">
                    <div
                      class="group relative flex flex-col items-center gap-2"
                    >
                      <TooltipProvider>
                        <Tooltip>
                          <TooltipTrigger as-child>
                            <Avatar
                              class="h-16 w-16 cursor-pointer"
                              @click="
                                open({ accept: 'image/*', multiple: false })
                              "
                            >
                              <template v-if="uploadTask">
                                <icon-lucide-loader class="animate-spin" />
                              </template>
                              <template v-else-if="uploadError">
                                <icon-lucide-alert-triangle />
                              </template>
                              <template v-else>
                                <AvatarImage
                                  :src="user?.photoURL as string"
                                  :alt="user?.displayName"
                                  referrerpolicy="no-referrer"
                                />
                                <AvatarFallback>
                                  {{ getInitials(user?.displayName as string) }}
                                </AvatarFallback>
                              </template>
                            </Avatar>
                          </TooltipTrigger>
                          <TooltipContent>
                            {{
                              uploadTask
                                ? `${uploadProgress ? (uploadProgress * 100).toFixed(0) : 0}%`
                                : "Upload profile picture"
                            }}
                          </TooltipContent>
                        </Tooltip>
                        <Tooltip>
                          <TooltipTrigger as-child>
                            <Button
                              v-if="photoURL"
                              class="border-background absolute top-0 right-0 size-4 rounded-full border-2 p-2 opacity-0 transition group-hover:opacity-100"
                              @click="photoURL = ''"
                            >
                              <icon-lucide-x />
                            </Button>
                          </TooltipTrigger>
                          <TooltipContent>
                            Remove profile picture
                          </TooltipContent>
                        </Tooltip>
                      </TooltipProvider>
                    </div>
                    <div class="grid gap-2">
                      <Label for="name" class="text-muted-foreground">
                        Preferred name
                      </Label>
                      <Input
                        id="name"
                        v-model="displayName"
                        label="Name"
                        placeholder="Your name"
                        class="h-8 w-64 focus:border-inherit focus:ring-0"
                      />
                    </div>
                  </div>
                  <Separator />
                  <div class="flex items-center gap-4">
                    <div class="flex flex-col gap-1">
                      <p class="leading-none font-medium">Primary email</p>
                      <p class="text-muted-foreground flex items-center gap-2">
                        {{ user?.email }}
                        <TooltipProvider v-if="user?.emailVerified">
                          <Tooltip>
                            <TooltipTrigger as-child>
                              <Badge
                                variant="outline"
                                class="gap-1 px-1 font-normal"
                              >
                                <icon-lucide-badge-check />
                                Verified
                              </Badge>
                            </TooltipTrigger>
                            <TooltipContent>
                              We've verified your email address.
                            </TooltipContent>
                          </Tooltip>
                        </TooltipProvider>
                      </p>
                    </div>
                    <div class="ml-auto flex gap-2">
                      <Button
                        v-if="!user?.emailVerified"
                        variant="secondary"
                        :disabled="sendingVerificationEmail"
                        class="gap-2"
                        @click="sendVerificationEmail"
                      >
                        <icon-lucide-loader
                          v-if="sendingVerificationEmail"
                          class="animate-spin"
                        />
                        Verify email
                      </Button>
                      <Dialog>
                        <DialogTrigger>
                          <Button variant="outline">
                            Change primary email
                          </Button>
                        </DialogTrigger>
                        <DialogContent>
                          <DialogHeader>
                            <DialogTitle> Change email </DialogTitle>
                            <DialogDescription>
                              Update your email address.
                            </DialogDescription>
                          </DialogHeader>
                          <div>
                            <Input
                              v-model="newEmail"
                              label="New email"
                              placeholder="Your new email address"
                              class="focus:border-inherit focus:ring-0"
                            />
                          </div>
                          <DialogFooter>
                            <Button
                              :disabled="changingEmail || !newEmail"
                              class="gap-2"
                              @click="changeEmail"
                            >
                              <icon-lucide-loader
                                v-if="changingEmail"
                                class="animate-spin"
                              />
                              Send verification email
                            </Button>
                          </DialogFooter>
                        </DialogContent>
                      </Dialog>
                    </div>
                  </div>
                  <div class="flex items-center gap-4">
                    <div class="flex flex-col gap-1">
                      <p class="leading-none font-medium">Connected accounts</p>
                      <p class="text-muted-foreground flex items-center gap-2">
                        Manage your connected accounts and sign-in methods.
                      </p>
                    </div>
                    <div class="ml-auto flex gap-2">
                      <Button variant="outline" class="gap-2">
                        Connect a new account
                      </Button>
                    </div>
                  </div>
                  <div
                    v-if="user?.providerData && user.providerData.length > 0"
                    class="flex flex-col-reverse gap-4"
                  >
                    <div
                      v-for="provider in user?.providerData"
                      :key="provider.providerId"
                      class="flex items-center gap-4"
                    >
                      <div class="relative">
                        <Avatar class="size-8">
                          <AvatarImage
                            :src="provider.photoURL as string"
                            :alt="provider.displayName"
                            referrerpolicy="no-referrer"
                          />
                          <AvatarFallback>
                            {{ getInitials(provider.displayName ?? "") }}
                          </AvatarFallback>
                        </Avatar>
                        <TooltipProvider>
                          <Tooltip>
                            <TooltipTrigger as-child>
                              <span
                                class="border-background bg-background absolute -right-1 -bottom-1 flex size-5 items-center justify-center rounded-full border-2"
                              >
                                <icon-logos-google-icon
                                  v-if="provider.providerId === 'google.com'"
                                  class="text-muted-foreground"
                                />
                                <icon-heroicons-check-badge-solid
                                  v-else-if="provider.providerId === 'password'"
                                  class="text-green-500"
                                />
                              </span>
                            </TooltipTrigger>
                            <TooltipContent>
                              {{ getComputedProviderName(provider.providerId) }}
                            </TooltipContent>
                          </Tooltip>
                        </TooltipProvider>
                      </div>
                      <div class="flex flex-col gap-1">
                        <p class="leading-none font-medium">
                          {{ provider.displayName }}
                        </p>
                        <p
                          class="text-muted-foreground flex items-center gap-2"
                        >
                          {{ provider.email }}
                        </p>
                      </div>
                      <div class="ml-auto flex gap-2">
                        <Button
                          v-if="provider.providerId === 'password'"
                          variant="secondary"
                          class="gap-2"
                        >
                          Change password
                        </Button>
                        <Button
                          :disabled="unlinkingProviderMap[provider.providerId]"
                          variant="destructive"
                          class="gap-2"
                          @click="unlinkProvider(provider.providerId)"
                        >
                          <icon-lucide-loader
                            v-if="unlinkingProviderMap[provider.providerId]"
                            class="animate-spin"
                          />
                          Disconnect
                        </Button>
                      </div>
                    </div>
                  </div>
                  <div v-else class="text-muted-foreground">
                    No connected accounts.
                  </div>
                  <Separator />
                  <div class="flex items-center gap-4">
                    <div class="flex flex-col gap-1">
                      <p class="leading-none font-medium">Delete account</p>
                      <p class="text-muted-foreground flex items-center gap-2">
                        Permanently delete your account.
                      </p>
                    </div>
                    <div class="ml-auto flex gap-2">
                      <AlertDialog>
                        <AlertDialogTrigger>
                          <Button variant="destructive" class="gap-2">
                            Delete account
                          </Button>
                        </AlertDialogTrigger>
                        <AlertDialogContent>
                          <AlertDialogHeader>
                            <AlertDialogTitle>
                              Delete account
                            </AlertDialogTitle>
                            <AlertDialogDescription>
                              Are you sure you want to delete your account?
                            </AlertDialogDescription>
                          </AlertDialogHeader>
                          <AlertDialogFooter>
                            <AlertDialogCancel>Cancel</AlertDialogCancel>
                            <AlertDialogAction
                              :disabled="deletingAccount"
                              variant="destructive"
                              @click="deleteAccount"
                            >
                              <icon-lucide-loader
                                v-if="deletingAccount"
                                class="animate-spin"
                              />
                              Delete account
                            </AlertDialogAction>
                          </AlertDialogFooter>
                        </AlertDialogContent>
                      </AlertDialog>
                    </div>
                  </div>
                </div>
              </TabsContent>
              <TabsContent value="appearance">
                <div class="flex h-full w-full flex-col gap-6 px-8 py-6">
                  <div class="flex flex-col">
                    <h3 class="text-lg font-semibold">Appearance</h3>
                    <p class="text-muted-foreground flex items-center gap-2">
                      Customize the appearance of the app.
                    </p>
                  </div>
                  <Separator />
                  <div class="flex items-center gap-4">
                    <div class="flex flex-col gap-1">
                      <p class="leading-none font-medium">Theme</p>
                      <p class="text-muted-foreground flex items-center gap-2">
                        Customize how Hyperjump looks on your device.
                      </p>
                    </div>
                    <div class="ml-auto flex gap-2">
                      <Select v-model="store" :default-value="store">
                        <SelectTrigger class="h-9 gap-2">
                          <SelectValue placeholder="Select a theme" />
                        </SelectTrigger>
                        <SelectContent align="end">
                          <SelectItem
                            v-for="mode in themes"
                            :key="mode.id"
                            :value="mode.id"
                            class="gap-4"
                          >
                            {{ mode.name }}
                          </SelectItem>
                        </SelectContent>
                      </Select>
                    </div>
                  </div>
                  <div class="flex items-center gap-4">
                    <div class="flex flex-col gap-1">
                      <p class="leading-none font-medium">Accent color</p>
                      <p class="text-muted-foreground flex items-center gap-2">
                        Choose your preferred accent color.
                      </p>
                    </div>
                    <div class="ml-auto flex gap-2">
                      <Select v-model="accent" :default-value="accent">
                        <SelectTrigger class="h-9 gap-2">
                          <SelectValue placeholder="Select an accent color" />
                        </SelectTrigger>
                        <SelectContent align="end">
                          <SelectItem
                            v-for="color in accents"
                            :key="color.id"
                            :value="color.id"
                            class="gap-4"
                          >
                            {{ color.name }}
                          </SelectItem>
                        </SelectContent>
                      </Select>
                    </div>
                  </div>
                  <div class="flex items-center gap-4">
                    <div class="flex flex-col gap-1">
                      <p class="leading-none font-medium">Language</p>
                      <p class="text-muted-foreground flex items-center gap-2">
                        Choose your preferred language.
                      </p>
                    </div>
                    <div class="ml-auto flex gap-2">
                      <Select v-model="locale" :default-value="locale">
                        <SelectTrigger class="h-9 gap-2">
                          <SelectValue placeholder="Select a language" />
                        </SelectTrigger>
                        <SelectContent align="end">
                          <SelectItem
                            v-for="language in languages"
                            :key="language.id"
                            :value="language.id"
                            class="gap-4"
                          >
                            {{ language.name }}
                          </SelectItem>
                        </SelectContent>
                      </Select>
                    </div>
                  </div>
                </div>
              </TabsContent>
              <TabsContent value="general">
                <div class="flex h-full w-full flex-col gap-6 px-8 py-6">
                  <div class="flex flex-col">
                    <h3 class="text-lg font-semibold">General</h3>
                    <p class="text-muted-foreground flex items-center gap-2">
                      Manage your general settings and preferences.
                    </p>
                  </div>
                  <Separator />
                  <div class="flex items-center gap-4">
                    <div
                      class="group relative flex flex-col items-center gap-2"
                    ></div>
                  </div>
                </div>
              </TabsContent>
            </OverlayScrollbarsComponent>
          </main>
        </SidebarProvider>
      </Tabs>
      <DialogFooter class="sr-only">
        <Button type="submit"> Save changes </Button>
      </DialogFooter>
    </DialogContent>
  </Dialog>
</template>
