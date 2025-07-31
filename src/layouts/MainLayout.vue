<template>
  <q-layout view="hHh Lpr lFf">
    <!-- Header -->
    <q-header elevated class="bg-primary text-white">
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          @click="toggleLeftDrawer"
          aria-label="Menu"
          icon="menu"
        />

        <q-toolbar-title>
          Amanah POS
        </q-toolbar-title>

        <q-space />

        <!-- User dropdown -->
        <q-btn-dropdown
          v-model="isDropdownOpen"
          flat
          dense
          :label="userName"
          icon="account_circle"
        >
          <q-list>
            <q-item clickable v-close-popup @click="handleProfile">
              <q-item-section>
                <q-item-label>Profile</q-item-label>
              </q-item-section>
            </q-item>

            <q-item clickable v-close-popup @click="handleLogout">
              <q-item-section>
                <q-item-label>Logout</q-item-label>
              </q-item-section>
            </q-item>
          </q-list>
        </q-btn-dropdown>
      </q-toolbar>
    </q-header>

    <!-- Left Drawer -->
    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
      :width="200"
      :breakpoint="700"
      class="bg-grey-3"
    >
      <q-scroll-area class="fit">
        <q-list padding>
          <!-- Navigation Links -->
          <template v-for="(item, index) in navItems" :key="index">
            <q-item 
              clickable 
              v-ripple
              :active="currentRoute === item.route"
              @click="navigateTo(item.route)"
            >
              <q-item-section avatar>
                <q-icon :name="item.icon" />
              </q-item-section>
              <q-item-section>
                {{ item.label }}
              </q-item-section>
            </q-item>
          </template>
        </q-list>
      </q-scroll-area>
    </q-drawer>

    <!-- Page Content -->
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref, watch } from "vue";
import { useQuasar } from "quasar";
import { useRouter } from "vue-router";

interface NavItem {
  label: string;
  icon: string;
  route: string;
}

export default defineComponent({
  name: "AuthenticatedLayout",
  
  setup() {
    const LEFT_DRAWER_STORAGE_KEY = "amanah-pos.layout.left-drawer-open";
    const $q = useQuasar();
    const router = useRouter();
    
    const leftDrawerOpen = ref(
      JSON.parse(localStorage.getItem(LEFT_DRAWER_STORAGE_KEY) || 'false')
    );
    const isDropdownOpen = ref(false);
    const currentRoute = ref(router.currentRoute.value.path);
    
    // Placeholder for user data - replace with actual user data fetching
    const userName = ref("Admin User");
    
    // Navigation items - customize as needed
    const navItems: NavItem[] = [
      { label: "Dashboard", icon: "dashboard", route: "/dashboard" },
      { label: "Products", icon: "inventory_2", route: "/products" },
      { label: "Sales", icon: "point_of_sale", route: "/sales" },
      { label: "Customers", icon: "people", route: "/customers" },
      { label: "Reports", icon: "analytics", route: "/reports" },
      { label: "Settings", icon: "settings", route: "/settings" },
    ];
    
    const toggleLeftDrawer = () => {
      leftDrawerOpen.value = !leftDrawerOpen.value;
    };

    watch(leftDrawerOpen, (newValue) => {
      localStorage.setItem(LEFT_DRAWER_STORAGE_KEY, JSON.stringify(newValue));
    });

    // Watch route changes
    router.afterEach((to) => {
      currentRoute.value = to.path;
    });

    onMounted(() => {
      leftDrawerOpen.value = JSON.parse(
        localStorage.getItem(LEFT_DRAWER_STORAGE_KEY) || 'false'
      );

      if ($q.screen.lt.md) {
        leftDrawerOpen.value = false;
      }
      
      // Placeholder for fetching user data
      // fetchUserData();
    });

    // Navigation handler
    const navigateTo = (route: string) => {
      router.push(route);
      if ($q.screen.lt.md) {
        leftDrawerOpen.value = false;
      }
    };

    // Placeholder methods - implement as needed
    const handleProfile = () => {
      router.push('/profile');
    };

    const handleLogout = () => {
      // Placeholder for logout logic
      console.log('Logout clicked');
      // await authService.logout();
      router.push('/login');
    };

    return {
      leftDrawerOpen,
      isDropdownOpen,
      userName,
      navItems,
      currentRoute,
      toggleLeftDrawer,
      navigateTo,
      handleProfile,
      handleLogout
    };
  }
});
</script>

<style lang="scss">
.q-drawer {
  .q-item {
    border-radius: 0 24px 24px 0;
    margin: 4px 8px;
    
    &--active {
      background: var(--q-primary);
      color: white;
    }
  }
}

.q-header {
  .q-toolbar {
    min-height: 64px;
  }
}

.q-page-container {
  padding-top: 20px;
  background: #f5f5f5;
}
</style>