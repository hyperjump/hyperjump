<script setup lang="ts">
const tasks = [
  {
    title: "Alert Dialog",
    description:
      "A modal dialog that interrupts the user with important content and expects a response.",
    href: "",
  },
  {
    title: "Hover Card",
    description:
      "For sighted users to preview content available behind a link.",
    href: "",
  },
  {
    title: "Progress",
    description:
      "Displays an indicator showing the completion progress of a task, typically displayed as a progress bar.",
    href: "",
  },
  {
    title: "Scroll-area",
    description: "Visually or semantically separates content.",
    href: "",
  },
  {
    title: "Tabs",
    description:
      "A set of layered sections of content—known as tab panels—that are displayed one at a time.",
    href: "",
  },
  {
    title: "Tooltip",
    description:
      "A popup that displays information related to an element when the element receives keyboard focus or the mouse hovers over it.",
    href: "",
  },
  {
    title: "Visually Hidden",
    description:
      "Hides content visually but remains accessible to screen readers.",
    href: "",
  },
  {
    title: "Dialog",
    description:
      "A modal dialog that interrupts the user with important content and expects a response.",
    href: "",
  },
  {
    title: "Disclosure",
    description:
      "A disclosure is a button that controls visibility of a section of content.",
    href: "",
  },
  {
    title: "Menu",
    description:
      "A type of composite widget that functions as a group of menuitem elements.",
    href: "",
  },
  {
    title: "Menu Button",
    description: "A button that controls the visibility of a menu.",
    href: "",
  },
  {
    title: "Radio Group",
    description:
      "A group of radio buttons that allows only one radio button to be selected.",
    href: "",
  },
  {
    title: "Switch",
    description:
      "A type of checkbox that represents on/off values, like a light switch.",
    href: "",
  },
  {
    title: "Tab",
    description: "A single panel in a set of tab panels.",
    href: "",
  },
  {
    title: "Tab Group",
    description: "A group of tabs that functions as a composite widget.",
    href: "",
  },
  {
    title: "Tooltip",
    description:
      "A popup that displays information related to an element when the element receives keyboard focus or the mouse hovers over it.",
    href: "",
  },
  {
    title: "Visually Hidden",
    description:
      "Hides content visually but remains accessible to screen readers.",
    href: "",
  },
  {
    title: "Dialog",
    description:
      "A modal dialog that interrupts the user with important content and expects a response.",
    href: "",
  },
]
</script>

<template>
  <OverlayScrollbarsWrapper>
    <div class="grid p-2">
      <NavigationMenuLink
        v-for="(component, index) in tasks"
        :key="index"
        class="group relative p-0"
      >
        <Alert class="border-0 bg-transparent p-3">
          <icon-lucide-circle-check-big />
          <AlertTitle>
            {{ component.title }}
          </AlertTitle>
          <AlertDescription class="text-muted-foreground text-xs text-pretty">
            {{ component.description }}
          </AlertDescription>
          <div
            class="bg-background absolute top-2 right-2 z-10 hidden items-center gap-2 rounded-lg p-1 shadow-md group-hover:flex"
          >
            <ToggleGroup type="single" variant="outline" size="sm">
              <ToggleGroupItem value="saved">
                <icon-lucide-bookmark />
                <!-- Save -->
              </ToggleGroupItem>
              <ToggleGroupItem value="done">
                <icon-lucide-check />
                <!-- Done -->
              </ToggleGroupItem>
            </ToggleGroup>
          </div>
        </Alert>
      </NavigationMenuLink>
    </div>
  </OverlayScrollbarsWrapper>
</template>
