<script setup lang="ts">
const team = [
  {
    name: "Tom Cook",
    email: "tom.cook@example.com",
    href: "#",
    imageUrl:
      "https://images.unsplash.com/photo-1472099645785-5658abf4ff4e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80",
  },
  {
    name: "Whitney Francis",
    email: "whitney.francis@example.com",
    href: "#",
    imageUrl:
      "https://images.unsplash.com/photo-1517365830460-955ce3ccd263?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80",
  },
  {
    name: "Leonard Krasner",
    email: "leonard.krasner@example.com",
    href: "#",
    imageUrl:
      "https://images.unsplash.com/photo-1519345182560-3f2917c472ef?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80",
  },
  {
    name: "Floyd Miles",
    email: "floyd.miles@example.com",
    href: "#",
    imageUrl:
      "https://images.unsplash.com/photo-1463453091185-61582044d556?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80",
  },
  {
    name: "Emily Selman",
    email: "emily.selman@example.com",
    href: "#",
    imageUrl:
      "https://images.unsplash.com/photo-1502685104226-ee32379fefbe?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80",
  },
]
</script>

<template>
  <div class="grid divide-y">
    <div>
      <Card class="border-0 shadow-none">
        <CardHeader>
          <CardTitle
            class="flex items-center justify-between text-base font-medium"
          >
            <span class="truncate"> General </span>
          </CardTitle>
          <CardDescription>
            Configure the general settings of the project.
          </CardDescription>
        </CardHeader>
        <CardContent class="grid gap-8">
          <div class="grid w-full items-center gap-3">
            <Label for="project-name"> Project name </Label>
            <Input
              id="project-name"
              type="text"
              name="project-name"
              placeholder="Name"
            />
          </div>
          <div class="grid w-full items-center gap-3">
            <Label for="project-description"> Description </Label>
            <Textarea
              id="project-description"
              name="project-description"
              placeholder="Description"
            />
          </div>
        </CardContent>
        <CardFooter>
          <Button size="sm">Save</Button>
        </CardFooter>
      </Card>
    </div>
    <div>
      <Card class="border-0 shadow-none">
        <CardHeader>
          <CardTitle
            class="flex items-center justify-between text-base font-medium"
          >
            <span class="truncate"> Team </span>
          </CardTitle>
          <CardDescription>
            Manage the team members of the project.
          </CardDescription>
        </CardHeader>
        <CardContent class="grid gap-8">
          <div class="grid w-full items-center gap-3">
            <Label for="project-team"> Members </Label>
            <div class="flex flex-wrap gap-2">
              <Button
                v-for="person in team"
                :key="person.email"
                :href="person.href"
                variant="outline"
                class="rounded-full"
                size="icon"
              >
                <img
                  class="inline-block size-8 rounded-full"
                  :src="person.imageUrl"
                  :alt="person.name"
                />
              </Button>
              <Button variant="outline" class="rounded-full" size="icon">
                <icon-lucide-plus />
              </Button>
            </div>
          </div>
          <div class="grid w-full items-center gap-3">
            <Label for="project-sharing"> Sharing </Label>
            <RadioGroup
              name="sharing"
              default-value="public"
              class="grid gap-4"
            >
              <div class="flex gap-3">
                <RadioGroupItem id="public" value="public" />
                <Label for="public" class="flex flex-col gap-2">
                  <span class="truncate">Public to everyone </span>
                  <span class="text-secondary-foreground/70 text-xs">
                    Everyone with the link will see this project.
                  </span>
                </Label>
              </div>
              <div class="flex gap-3">
                <RadioGroupItem id="private" value="private" />
                <Label for="private" class="flex flex-col gap-2">
                  <span class="truncate">Private to project members </span>
                  <span class="text-secondary-foreground/70 text-xs">
                    Only members of this project would be able to access.
                  </span>
                </Label>
              </div>
              <div class="flex gap-3">
                <RadioGroupItem id="personal" value="personal" />
                <Label for="personal" class="flex flex-col gap-2">
                  <span class="truncate">Private to you </span>
                  <span class="text-secondary-foreground/70 text-xs">
                    You are the only one able to access this project.
                  </span>
                </Label>
              </div>
            </RadioGroup>
          </div>
        </CardContent>
      </Card>
    </div>
    <div>
      <Card class="border-0 shadow-none">
        <CardHeader>
          <CardTitle
            class="flex items-center justify-between text-base font-medium"
          >
            <span class="truncate"> Notifications </span>
          </CardTitle>
          <CardDescription>
            Configure the notifications of the project.
          </CardDescription>
        </CardHeader>
        <CardContent class="grid gap-8">
          <div class="grid gap-6">
            <div class="flex gap-3">
              <Label for="triggers" class="flex flex-col gap-2">
                <span class="truncate">Task updates</span>
                <span class="text-secondary-foreground/70 text-xs">
                  When an automation completes, fails, or encounters an error.
                </span>
              </Label>
              <Switch id="triggers" value="triggers" />
            </div>
            <div class="flex gap-3">
              <Label for="enabled" class="flex flex-col gap-2">
                <span class="truncate">Workflow execution</span>
                <span class="text-secondary-foreground/70 text-xs">
                  Start, pause, or completion of multi-step workflows.
                </span>
              </Label>
              <Switch id="enabled" value="enabled" />
            </div>
            <div class="flex gap-3">
              <Label for="agent" class="flex flex-col gap-2">
                <span class="truncate">Agent Insights</span>
                <span class="text-secondary-foreground/70 text-xs">
                  AI-generated suggestions, optimizations, or reports.
                </span>
              </Label>
              <Switch id="agent" value="agent" />
            </div>
            <div class="flex gap-3">
              <Label for="collaboration" class="flex flex-col gap-2">
                <span class="truncate">Collaboration Alerts</span>
                <span class="text-secondary-foreground/70 text-xs">
                  When a team member assigns a task, comments, or makes updates.
                </span>
              </Label>
              <Switch id="collaboration" value="collaboration" />
            </div>
            <div class="flex gap-3">
              <Label for="security" class="flex flex-col gap-2">
                <span class="truncate">Security & Compliance</span>
                <span class="text-secondary-foreground/70 text-xs">
                  Unauthorized access attempts, unusual activity, or system
                  failures.
                </span>
              </Label>
              <Switch id="security" value="security" />
            </div>
          </div>
        </CardContent>
      </Card>
    </div>
    <div>
      <Card class="border-0 shadow-none">
        <CardHeader>
          <CardTitle
            class="flex items-center justify-between text-base font-medium"
          >
            <span class="truncate"> Danger Zone </span>
          </CardTitle>
          <CardDescription>
            Delete the project and all its data permanently.
          </CardDescription>
        </CardHeader>
        <CardFooter>
          <Button variant="secondary" size="sm">Delete workflow</Button>
        </CardFooter>
      </Card>
    </div>
  </div>
</template>
