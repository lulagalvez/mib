<nav>
	<a href="/">Hogar</a>
	<a href="/about">Acerca de</a>
	<a href="/settings">Configuración</a>
</nav>

<slot />

<style>
	/* Style for the navigation */
	nav {
		background-color: #333;
		padding: 10px;
		display: flex;
		justify-content: space-around;
	}

	/* Style for the navigation links */
	nav a {
		color: #fff;
		text-decoration: none;
		padding: 8px 16px;
		border-radius: 4px;
		transition: background-color 0.3s ease;
	}

	/* Hover effect for navigation links */
	nav a:hover {
		background-color: #555;
	}

	/* Styling for the content within the layout */
	slot {
		margin-top: 20px;
		padding: 20px;
		border: 1px solid #ccc;
		border-radius: 4px;
	}
</style>
