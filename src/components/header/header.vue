<template>
    <header>
        Lesson Booking System
        <nav>
            <router-link to="/">Home</router-link> |
            <router-link to="/about">About</router-link>
        </nav>
        <router-view/>
    </header>
</template>

<script>
/* eslint-disable */
export default {
    name: 'Header',
}
</script>