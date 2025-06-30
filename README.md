# Image-Gallery
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Image Gallery</title>
  <style>
    :root {
     --bg: linear-gradient(to right, #fdf0e0, #e6d3c6);
      --accent:#7f230d;
      --highlight: #2a0f07;
      --text: #4b3832;
      --button-bg: #f8e8df;
      --input-bg: #f3e5dc;
    }

    body {
      margin: 0;
      font-family: 'Poppins', sans-serif;
      background: var(--bg);
      color: var(--text);
    }

    h1 {
      text-align: center;
      padding: 2rem 1rem 1rem;
      color: var(--highlight);
      background: -webkit-linear-gradient(45deg, var(--accent), var(--highlight));
     -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    .controls {
      text-align: center;
      margin-bottom: 2rem;
    }

    .controls button {
      background: var(--button-bg);
      border: 2px solid var(--highlight);
      color: var(--highlight);
      padding: 0.6rem 1.2rem;
      margin: 0.4rem;
      border-radius: 30px;
      font-weight: bold;
      cursor: pointer;
      transition: all 0.3s ease;
    }

    .controls button:hover {
      background-color: var(--highlight);
      color: #fff;
      transform: scale(1.1);
    }

    .controls input[type="text"] {
      padding: 0.6rem;
      border-radius: 30px;
      border: 2px solid #ccc;
      background: var(--input-bg);
      color: var(--text);
      width: 260px;
      max-width: 90%;
      outline: none;
    }

    .grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 1.5rem;
      padding: 0 1.5rem 2rem;
    }

    .grid figure {
      margin: 0;
      position: relative;
    }

    .grid img {
      width: 80%;
      border-radius: 20px;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
      transition: transform 0.4s, filter 0.4s;
      cursor: pointer;
    }
.grid img {
  height: 300px;
  object-fit: cover;
}

    .grid img:hover {
      transform: scale(1.05);
      filter: brightness(1.05) contrast(1.05);
    }

    .grid figcaption {
      text-align: center;
      font-size: 0.95rem;
      margin-top: 0.5rem;
      color: var(--highlight);
      font-weight: 500;
    }

    .lightbox {
      display: none;
      position: fixed;
      top: 0; left: 0;
      width: 100vw; height: 100vh;
      background: rgba(0,0,0,0.8);
      justify-content: center;
      align-items: center;
      flex-direction: column;
      z-index: 9999;
    }

    .lightbox img {
      max-width: 90%;
      max-height: 80%;
      border-radius: 20px;
      box-shadow: 0 12px 35px rgba(0,0,0,0.5);
    }

    .nav-btns {
      font-size: 2.5rem;
      color: var(--accent);
      cursor: pointer;
      user-select: none;
      margin: 20px;
    }

    .nav-btns:hover {
      color: var(--highlight);
    }

    @media (max-width: 600px) {
      .nav-btns {
        font-size: 2rem;
      }
    }
  </style>
</head>
<body>
  <h1>🧡Image Gallery</h1>

  <div class="controls">
    <div style="margin-bottom: 1rem;">
      <button onclick="filterImages('all')">All</button>
      <button onclick="filterImages('nature')">Nature</button>
      <button onclick="filterImages('food')">Food</button>
      <button onclick="filterImages('people')">People</button>
    </div>
    <input type="text" id="searchInput" placeholder="Search images..." oninput="searchImages()">
  </div>

  <div class="grid" id="gallery">
    <figure><img src="https://img.freepik.com/premium-photo/close-up-pink-leaves_1048944-19598570.jpg" alt="leafs with raindrops" data-category="nature"><figcaption>Leaf Kissed by Rain</figcaption></figure>
    <figure><img src="https://i.pinimg.com/236x/eb/7d/04/eb7d046314c3620e864fcfa61b32cd7b.jpg" alt="Cocoa Dusk" data-category="food"><figcaption>Cocoa Dusk</figcaption></figure>
    <figure><img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTe55FvkRWDKP2AMWLevcmvNNGbAycqtSPskQ&s" alt="golden hours" data-category="people"><figcaption>Golden Hours</figcaption></figure>
    <figure><img src="https://images.stockcake.com/public/d/7/e/d7eaa506-abda-46a7-99fd-562313950077_large/misty-river-scenery-stockcake.jpg" alt="River" data-category="nature"><figcaption>Dewlit River</figcaption></figure>
    <figure><img src="https://images.prestigeonline.com/wp-content/uploads/sites/6/2021/12/21124710/sam-moqadam-yxZSAjyToP4-unsplash-scaled-1.jpg" alt="pancakes" data-category="food"><figcaption>Maple Drizzle</figcaption></figure>
    <figure><img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRpagvY8W5F-axetMd3agY-cQOWDV5b2UEVeA&s" alt="Man" data-category="people"><figcaption>Evening Stroll</figcaption></figure>
  </div>

  <div class="lightbox" id="lightbox">
    <div class="nav-btns" onclick="navigate(-1)">&#10094;</div>
    <img id="lightbox-img" src="" alt="" />
    <div class="nav-btns" onclick="navigate(1)">&#10095;</div>
  </div>

  <script>
  const allImages = document.querySelectorAll('#gallery img');
  const lightbox = document.getElementById('lightbox');
  const lightboxImg = document.getElementById('lightbox-img');
  const searchInput = document.getElementById('searchInput');

  let visibleImages = [];
  let currentIndex = 0;

  // Build visible image list based on what's shown
  function updateVisibleImages() {
    visibleImages = Array.from(allImages).filter(img => {
      return img.closest('figure').style.display !== 'none';
    });
  }

  // Click image to open lightbox
  allImages.forEach((img) => {
    img.addEventListener('click', () => {
      updateVisibleImages();
      currentIndex = visibleImages.indexOf(img);
      if (currentIndex !== -1) {
        lightboxImg.src = visibleImages[currentIndex].src;
        lightbox.style.display = 'flex';
      }
    });
  });

  // Navigate next/prev in lightbox
  function navigate(direction) {
    if (!visibleImages.length) return;
    currentIndex = (currentIndex + direction + visibleImages.length) % visibleImages.length;
    lightboxImg.src = visibleImages[currentIndex].src;
  }

  // Close lightbox on background click
  lightbox.addEventListener('click', (e) => {
    if (e.target !== lightboxImg) {
      lightbox.style.display = 'none';
    }
  });

  // Filter by category
  function filterImages(category) {
    const figures = document.querySelectorAll("#gallery figure");
    figures.forEach(fig => {
      const img = fig.querySelector("img");
      fig.style.display = (category === 'all' || img.dataset.category === category)
        ? 'block'
        : 'none';
    });
    searchInput.value = '';
  }

  // Search by alt text or caption
  function searchImages() {
    const query = searchInput.value.toLowerCase();
    const figures = document.querySelectorAll("#gallery figure");
    figures.forEach(fig => {
      const img = fig.querySelector("img");
      const caption = fig.querySelector("figcaption").textContent.toLowerCase();
      const alt = img.alt.toLowerCase();
      const match = alt.includes(query) || caption.includes(query);
      fig.style.display = match ? 'block' : 'none';
    });
  }

  // Expose navigate to window so inline onclick works
  window.navigate = navigate;
  

  </script>
</body>
</html>
