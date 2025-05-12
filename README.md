<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Book My Show - Clone</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background-color: #121212;
      color: #fff;
      margin: 0;
      padding: 0;
    }

    header {
      background-color: #e50914;
      padding: 1em;
      text-align: center;
      font-size: 24px;
      font-weight: bold;
    }

    .container {
      padding: 20px;
    }

    .movies {
      display: flex;
      flex-wrap: wrap;
      gap: 20px;
      justify-content: center;
    }

    .movie-card {
      background: #1e1e1e;
      padding: 10px;
      border-radius: 10px;
      width: 180px;
      text-align: center;
      transition: transform 0.2s;
      cursor: pointer;
    }

    .movie-card:hover {
      transform: scale(1.05);
    }

    .movie-card img {
      width: 100%;
      height: auto;
      border-radius: 10px;
    }

    .showtimes, .seat-selection, .review-section, .confirmation {
      margin-top: 30px;
      display: none;
    }

    .btn {
      background-color: #e50914;
      border: none;
      padding: 10px 20px;
      margin-top: 10px;
      color: white;
      border-radius: 6px;
      cursor: pointer;
    }

    .seats {
      display: grid;
      grid-template-columns: repeat(8, 1fr);
      gap: 10px;
      margin-top: 20px;
    }

    .seat {
      width: 30px;
      height: 30px;
      background-color: #444;
      border-radius: 5px;
      cursor: pointer;
      text-align: center;
      line-height: 30px;
    }

    .seat.selected {
      background-color: #e50914;
    }

    textarea, input[type="number"], input[type="datetime-local"] {
      width: 100%;
      margin-top: 10px;
      padding: 10px;
      border-radius: 6px;
      border: none;
    }
  </style>
</head>
<body>

<header>Book My Show - Clone</header>

<div class="container">

  <!-- Movie List -->
  <div class="movies" id="movie-list">
    <div class="movie-card" onclick="selectMovie('Avatar')">
      <img src="Avathar.jpeg" alt="Avatar" />
      <p>Avatar</p>
    </div>

    <div class="movie-card" onclick="selectMovie('Inception')">
      <img src="Inception.webp" alt="Inception" />
      <p>Inception</p>
    </div>

    <div class="movie-card" onclick="selectMovie('Guntur Kaaram')">
      <img src="guntur-kaaram-et00310760-1686726453.avif" alt="Guntur Kaaram" />
      <p>Guntur Kaaram</p>
    </div>
  </div>

  <!-- Showtimes -->
  <div class="showtimes" id="showtimes">
    <h2 id="selected-movie-title">Selected Movie</h2>
    <p>Select a show time:</p>
    <input type="datetime-local" id="showtime">
    <button class="btn" onclick="chooseShowTime()">Continue</button>
  </div>

  <!-- Seat Selection -->
  <div class="seat-selection" id="seat-selection">
    <h2>Select Your Seats</h2>
    <div class="seats" id="seats"></div>
    <button class="btn" onclick="confirmBooking()">Confirm Booking</button>
  </div>

  <!-- Review Section -->
  <div class="review-section" id="review-section">
    <h2>⭐ Review Your Experience</h2>
    <input id="rating" type="number" min="1" max="5" placeholder="Rating (1-5)" />
    <textarea id="review" rows="4" placeholder="Write your review..."></textarea>
    <button class="btn" onclick="submitReview()">Submit Review</button>
  </div>

  <!-- Confirmation -->
  <div class="confirmation" id="confirmation">
    <h2>✅ Booking Confirmed!</h2>
    <p id="booking-details"></p>
  </div>

</div>

<script>
  let selectedMovie = '';
  let selectedShowTime = '';
  let selectedSeats = [];

  function selectMovie(movieName) {
    selectedMovie = movieName;
    document.getElementById('selected-movie-title').innerText = movieName;
    document.getElementById('showtimes').style.display = 'block';
    document.getElementById('seat-selection').style.display = 'none';
    document.getElementById('review-section').style.display = 'none';
    document.getElementById('confirmation').style.display = 'none';
  }

  function chooseShowTime() {
    const showtimeInput = document.getElementById("showtime").value;
    if (!showtimeInput) {
      alert("Please select a showtime.");
      return;
    }
    selectedShowTime = new Date(showtimeInput);
    document.getElementById('seat-selection').style.display = 'block';
    generateSeats();
  }

  function generateSeats() {
    const seatsContainer = document.getElementById('seats');
    seatsContainer.innerHTML = '';
    selectedSeats = [];
    for (let i = 1; i <= 32; i++) {
      const seat = document.createElement('div');
      seat.className = 'seat';
      seat.innerText = i;
      seat.onclick = () => toggleSeat(seat, i);
      seatsContainer.appendChild(seat);
    }
  }

  function toggleSeat(seatDiv, seatNumber) {
    if (selectedSeats.includes(seatNumber)) {
      selectedSeats = selectedSeats.filter(s => s !== seatNumber);
      seatDiv.classList.remove('selected');
    } else {
      selectedSeats.push(seatNumber);
      seatDiv.classList.add('selected');
    }
  }

  function confirmBooking() {
    if (selectedSeats.length === 0) {
      alert("Please select at least one seat.");
      return;
    }

    const details = `
      Movie: ${selectedMovie}<br>
      Showtime: ${selectedShowTime.toLocaleString()}<br>
      Seats: ${selectedSeats.join(", ")}
    `;

    document.getElementById('booking-details').innerHTML = details;
    document.getElementById('confirmation').style.display = 'block';
    document.getElementById('review-section').style.display = 'none';

    setTimeout(() => {
      const now = new Date();
      if (now >= selectedShowTime) {
        document.getElementById('review-section').style.display = 'block';
      } else {
        checkReviewAvailability();
      }
    }, 1000);
  }

  function checkReviewAvailability() {
    const now = new Date();
    if (now >= selectedShowTime) {
      document.getElementById('review-section').style.display = 'block';
    } else {
      setTimeout(checkReviewAvailability, 60000);
    }
  }

  function submitReview() {
    const rating = parseInt(document.getElementById("rating").value);
    const review = document.getElementById("review").value;

    if (!rating || rating < 1 || rating > 5 || !review.trim()) {
      alert("Please enter a valid rating (1-5) and review.");
      return;
    }

    alert(`Thanks for reviewing ${selectedMovie}!`);
    document.getElementById('review-section').style.display = 'none';
    document.getElementById('rating').value = '';
    document.getElementById('review').value = '';
  }
</script>

</body>
</html>
