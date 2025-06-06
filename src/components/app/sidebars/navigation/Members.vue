<script setup lang="ts">
import Avatar from "vue-boring-avatars"

const agents = ref([
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
  { id: 3, name: "Charlie" },
])

const messages = ref([
  { role: "agent", content: "Hi, how can I help you today?" },
  { role: "user", content: "Hey, I'm having trouble with my account." },
  { role: "agent", content: "What seems to be the problem?" },
  { role: "user", content: "I can't log in." },
  { role: "agent", content: "Let me check that for you." },
  { role: "user", content: "Thanks!" },
  { role: "agent", content: "You're welcome! Is there anything else?" },
  { role: "user", content: "Actually, I have another question." },
  { role: "agent", content: "Sure, what's your question?" },
  { role: "user", content: "Can you help me with my billing?" },
  { role: "agent", content: "Of course! What do you need help with?" },
  { role: "user", content: "I need to update my payment method." },
  { role: "agent", content: "No problem, I can assist you with that." },
  { role: "user", content: "Great, thank you!" },
  {
    role: "agent",
    content: "You're welcome! Let me know if you need anything else.",
  },
  { role: "user", content: "I will, thanks!" },
  { role: "agent", content: "Have a great day!" },
  { role: "user", content: "You too!" },
  { role: "agent", content: "Goodbye!" },
])
</script>

<template>
  <div id="tour-team-members" class="flex flex-col gap-2">
    <TooltipProvider>
      <Tooltip v-for="agent in agents" :key="agent.id">
        <Sheet>
          <SheetTrigger as-child>
            <TooltipTrigger
              class="group relative flex aspect-square items-center justify-center rounded-md transition"
            >
              <Avatar
                :name="`Agent ${agent.id}`"
                :size="14"
                variant="beam"
                :title="false"
                :square="true"
                :colors="[
                  'var(--chart-5)',
                  'var(--chart-4)',
                  'var(--chart-3)',
                  'var(--chart-1)',
                  'var(--chart-1)',
                ]"
                class="group-hover:ring-primary group-hover:ring-offset-sidebar rounded-full p-0 grayscale transition group-hover:ring-2 group-hover:ring-offset-2 group-hover:grayscale-0"
              />
              <span
                class="ring-sidebar ring-offset-sidebar absolute right-1.5 bottom-1.5 block size-1.5 rounded-full bg-green-500 ring-2"
              ></span>
            </TooltipTrigger>
            <TooltipContent side="right"> {{ agent.name }} </TooltipContent>
          </SheetTrigger>
          <SheetContent class="m-3 h-auto gap-0 rounded-md border" side="left">
            <SheetHeader>
              <SheetTitle>{{ agent.name }}</SheetTitle>
              <SheetDescription>
                Chat with {{ agent.name }} to get assistance with your tasks.
              </SheetDescription>
            </SheetHeader>
            <Separator />
            <div class="flex grow flex-col overflow-auto overscroll-none">
              <div class="grid grid-cols-1 gap-4 p-4">
                <div
                  v-for="(message, index) in messages"
                  :key="index"
                  :class="[
                    'flex w-max max-w-3/4 flex-col rounded-2xl px-3 py-2',
                    message.role === 'user'
                      ? 'bg-primary text-primary-foreground ml-auto rounded-br-md'
                      : 'bg-muted rounded-bl-md',
                  ]"
                >
                  {{ message.content }}
                </div>
              </div>
            </div>
            <Separator />
            <SheetFooter>
              <div class="flex items-center justify-between gap-2">
                <Input placeholder="Type a message..." />
                <Button>
                  <icon-lucide-send-horizontal />
                </Button>
              </div>
            </SheetFooter>
          </SheetContent>
        </Sheet>
      </Tooltip>
    </TooltipProvider>
  </div>
</template>
