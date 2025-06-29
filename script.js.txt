document.addEventListener('DOMContentLoaded', () => {

    // --- INSECURE CLIENT-SIDE LOGIN LOGIC ---
    // DO NOT USE THIS FOR REAL SECURITY. IT CAN BE EASILY BYPASSED.
    const loginForm = document.getElementById('login-form');
    const loginOverlay = document.getElementById('login-overlay');
    const portfolioContent = document.getElementById('portfolio-content');
    const logoutBtn = document.getElementById('logout-btn'); // Get the new logout button
    const heroElements = document.querySelectorAll('.hero-section .greeting, .hero-section .name, .hero-section .role, .hero-section img');


    // Function to show portfolio and animate hero section
    const showPortfolioAndAnimateHero = () => {
        loginOverlay.style.display = 'none';
        portfolioContent.classList.remove('hidden');
        heroElements.forEach(element => {
            element.classList.add('animate-visible');
        });
    };

    // Check if user is already "logged in" (e.g., from a previous session)
    if (sessionStorage.getItem('loggedIn') === 'true') {
        showPortfolioAndAnimateHero();
    } else {
        loginOverlay.style.display = 'flex'; // Ensure it's visible if not logged in
        portfolioContent.classList.add('hidden'); // Ensure content is hidden
    }

    if (loginForm) {
        loginForm.addEventListener('submit', function(e) {
            e.preventDefault(); // Prevent default form submission

            const usernameInput = document.getElementById('username');
            const passwordInput = document.getElementById('password');

            const username = usernameInput.value.trim();
            const password = passwordInput.value.trim();

            // Hardcoded credentials for demonstration (INSECURE!)
            if (username === 'jomarie' && password === 'jomarie4') {
                sessionStorage.setItem('loggedIn', 'true'); // "Log in" the user
                showPortfolioAndAnimateHero(); // Show and animate
            } else {
                // Using a simple alert for demonstration, in a real app, use a custom modal
                alert('Invalid username or password. (Hint: jomarie / jomarie4)');
                // Clear password field for security
                passwordInput.value = '';
            }
        });
    }

    // --- Logout Logic ---
    if (logoutBtn) {
        logoutBtn.addEventListener('click', function() {
            sessionStorage.removeItem('loggedIn'); // Remove the login status
            loginOverlay.style.display = 'flex'; // Show the login overlay
            portfolioContent.classList.add('hidden'); // Hide portfolio content
            // Optional: Scroll to the top of the page after logout
            window.scrollTo({ top: 0, behavior: 'smooth' });

            // Remove animation classes from hero elements so they can animate again on next login
            heroElements.forEach(element => {
                element.classList.remove('animate-visible');
            });
        });
    }


    // --- Smooth scroll for internal anchor links ---
    document.querySelectorAll('a[href^="#"]').forEach(anchor => {
        anchor.addEventListener('click', function(e) {
            e.preventDefault(); // Prevent default jump behavior

            const targetId = this.getAttribute('href');
            const targetElement = document.querySelector(targetId);

            if (targetElement) {
                // Adjust scroll position to account for fixed navbar height
                const navbarHeight = document.querySelector('.navbar').offsetHeight;
                const offsetTop = targetElement.offsetTop - navbarHeight;

                window.scrollTo({
                    top: offsetTop,
                    behavior: 'smooth'
                });
            }
        });
    });

    // --- Form validation for contact form ---
    const contactForm = document.querySelector('#contact-form');
    if (contactForm) {
        contactForm.addEventListener('submit', function(e) {
            const name = document.querySelector('#name');
            const email = document.querySelector('#email');
            const message = document.querySelector('#message');

            if (!name.value.trim() || !email.value.trim() || !message.value.trim()) {
                e.preventDefault();
                alert('Please fill out all fields.');
            } else if (!validateEmail(email.value)) {
                e.preventDefault();
                alert('Please enter a valid email address.');
            }
        });
    }

    // Basic email format validation
    function validateEmail(email) {
        const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        return regex.test(email);
    }

    // --- Intersection Observer for "on-scroll" animations ---
    // Note: Hero elements are now handled by showPortfolioAndAnimateHero() on load/login
    const animatedSections = document.querySelectorAll('section'); // Only observe sections here

    const observerOptions = {
        root: null, // Use the viewport as the root
        rootMargin: '0px',
        threshold: 0.1 // Trigger when 10% of the element is visible
    };

    const elementObserver = new IntersectionObserver((entries, observer) => {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                // Add a class that triggers the CSS animation
                entry.target.classList.add('animate-visible');
                // Stop observing once animated if you only want it to animate once
                observer.unobserve(entry.target);
            }
        });
    }, observerOptions);

    // Observe each relevant section
    animatedSections.forEach(element => {
        elementObserver.observe(element);
    });


    // --- Highlight current section in navigation as you scroll ---
    const sections = document.querySelectorAll('section');
    const navLinks = document.querySelectorAll('.navbar ul li a, footer nav ul li a');
    const mainNavbar = document.querySelector('.navbar');

    window.addEventListener('scroll', () => {
        let currentSectionId = '';
        const scrollY = window.pageYOffset;
        const navbarHeight = mainNavbar.offsetHeight; // Get current navbar height

        sections.forEach(section => {
            // Adjust sectionTop to account for navbar height, so activation is accurate
            const sectionTop = section.offsetTop - navbarHeight - 50; // Added extra 50px buffer
            const sectionHeight = section.offsetHeight;

            if (scrollY >= sectionTop && scrollY < sectionTop + sectionHeight) {
                currentSectionId = section.getAttribute('id');
            }
        });

        navLinks.forEach(link => {
            // Ensure the logout button isn't accidentally styled as active
            if (!link.classList.contains('logout-btn')) { // Exclude logout button
                link.classList.remove('active');
            }
            if (link.getAttribute('href') === `#${currentSectionId}`) {
                link.classList.add('active');
            }
        });
    });

}); // End of DOMContentLoaded
