<script>
  import TrustBar from "$lib/TrustBar.svelte";
  import ToolHeader from "$lib/ToolHeader.svelte";
  import ToolFooter from "$lib/ToolFooter.svelte";

  let identity = $state(null);
  let copied = $state(false);
  let copiedField = $state("");

  const FIRST_NAMES_M = [
    "James",
    "Robert",
    "Michael",
    "William",
    "David",
    "Richard",
    "Joseph",
    "Thomas",
    "Daniel",
    "Matthew",
    "Andrew",
    "Joshua",
    "Christopher",
    "Nathan",
    "Ryan",
    "Kevin",
    "Brian",
    "Eric",
    "Patrick",
    "Sean",
  ];
  const FIRST_NAMES_F = [
    "Mary",
    "Patricia",
    "Jennifer",
    "Linda",
    "Sarah",
    "Jessica",
    "Emily",
    "Hannah",
    "Ashley",
    "Megan",
    "Rachel",
    "Olivia",
    "Samantha",
    "Lauren",
    "Nicole",
    "Amber",
    "Sophia",
    "Grace",
    "Chloe",
    "Natalie",
  ];
  const LAST_NAMES = [
    "Smith",
    "Johnson",
    "Williams",
    "Brown",
    "Jones",
    "Garcia",
    "Miller",
    "Davis",
    "Rodriguez",
    "Martinez",
    "Wilson",
    "Anderson",
    "Thomas",
    "Jackson",
    "White",
    "Harris",
    "Clark",
    "Lewis",
    "Young",
    "Walker",
  ];
  const STREETS = [
    "Oak",
    "Maple",
    "Cedar",
    "Pine",
    "Elm",
    "Main",
    "Park",
    "Lake",
    "Hill",
    "Sunset",
    "River",
    "Forest",
    "Meadow",
    "Spring",
    "Valley",
  ];
  const STREET_TYPES = ["St", "Ave", "Blvd", "Dr", "Ln", "Rd", "Way", "Ct"];
  const CITIES = [
    { city: "Austin", state: "TX", zip: "787" },
    { city: "Portland", state: "OR", zip: "972" },
    { city: "Denver", state: "CO", zip: "802" },
    { city: "Seattle", state: "WA", zip: "981" },
    { city: "Nashville", state: "TN", zip: "372" },
    { city: "Raleigh", state: "NC", zip: "276" },
    { city: "Phoenix", state: "AZ", zip: "850" },
    { city: "Chicago", state: "IL", zip: "606" },
    { city: "Atlanta", state: "GA", zip: "303" },
    { city: "Boston", state: "MA", zip: "021" },
    { city: "San Diego", state: "CA", zip: "921" },
    { city: "Minneapolis", state: "MN", zip: "554" },
  ];
  const DOMAINS = [
    "gmail.com",
    "yahoo.com",
    "outlook.com",
    "proton.me",
    "icloud.com",
    "mail.com",
  ];
  const JOBS = [
    "Software Engineer",
    "Product Manager",
    "Designer",
    "Analyst",
    "Consultant",
    "Teacher",
    "Nurse",
    "Writer",
    "Accountant",
    "Architect",
    "Marketing Manager",
    "Sales Representative",
    "Data Scientist",
    "Project Manager",
    "Mechanic",
  ];
  const COMPANIES = [
    "Acme Corp",
    "Globex Inc",
    "Initech",
    "Umbrella Co",
    "Stark Industries",
    "Wayne Enterprises",
    "Cyberdyne",
    "Soylent Corp",
    "Hooli",
    "Pied Piper",
    "Vandelay Industries",
    "Prestige Worldwide",
  ];

  function rand(arr) {
    return arr[Math.floor(Math.random() * arr.length)];
  }

  function randInt(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  function padZero(n, len = 2) {
    return String(n).padStart(len, "0");
  }

  function generate() {
    const isMale = Math.random() > 0.5;
    const first = rand(isMale ? FIRST_NAMES_M : FIRST_NAMES_F);
    const last = rand(LAST_NAMES);
    const loc = rand(CITIES);
    const birthYear = randInt(1970, 2002);
    const birthMonth = randInt(1, 12);
    const birthDay = randInt(1, 28);
    const usernameNum = randInt(10, 999);
    const username = `${first.toLowerCase()}${last.toLowerCase().charAt(0)}${usernameNum}`;
    const emailDomain = rand(DOMAINS);

    identity = {
      name: `${first} ${last}`,
      gender: isMale ? "Male" : "Female",
      birthday: `${padZero(birthMonth)}/${padZero(birthDay)}/${birthYear}`,
      age: new Date().getFullYear() - birthYear,
      address: `${randInt(100, 9999)} ${rand(STREETS)} ${rand(STREET_TYPES)}`,
      city: loc.city,
      state: loc.state,
      zip: `${loc.zip}${padZero(randInt(10, 99))}`,
      phone: `(${randInt(200, 999)}) ${randInt(200, 999)}-${padZero(randInt(1000, 9999), 4)}`,
      email: `${username}@${emailDomain}`,
      username: username,
      job: rand(JOBS),
      company: rand(COMPANIES),
      ssn: `${randInt(100, 899)}-${padZero(randInt(10, 99))}-${padZero(randInt(1000, 9999), 4)}`,
    };
    copied = false;
  }

  function copyAll() {
    if (!identity) return;
    const text = Object.entries(identity)
      .map(([k, v]) => `${k}: ${v}`)
      .join("\n");
    navigator.clipboard.writeText(text).then(() => {
      copied = true;
      setTimeout(() => (copied = false), 1500);
    });
  }

  function copyField(key, value) {
    navigator.clipboard.writeText(value).then(() => {
      copiedField = key;
      setTimeout(() => (copiedField = ""), 1200);
    });
  }

  const FIELD_LABELS = {
    name: "Full Name",
    gender: "Gender",
    birthday: "Birthday",
    age: "Age",
    address: "Street",
    city: "City",
    state: "State",
    zip: "ZIP",
    phone: "Phone",
    email: "Email",
    username: "Username",
    job: "Job Title",
    company: "Company",
    ssn: "SSN",
  };

  // Generate on mount
  $effect(() => {
    generate();
  });
</script>

<TrustBar sourceUrl="https://github.com/Griffin2/localid" />

<div class="page-shell">
  <ToolHeader
    title="Fake Identity"
    description="Generate realistic test data — no API, no server, all random."
  />

  {#if identity}
    <div class="identity-card">
      {#each Object.entries(identity) as [key, value]}
        <div
          class="field-row"
          onclick={() => copyField(key, String(value))}
          role="button"
          tabindex="0"
          title="Click to copy"
        >
          <span class="field-label">{FIELD_LABELS[key] || key}</span>
          <span class="field-value">{value}</span>
          <span class="field-copy">
            {copiedField === key ? "✓" : "⎘"}
          </span>
        </div>
      {/each}
    </div>
  {/if}

  <div class="controls">
    <button class="btn btn-primary" onclick={generate}>↻ New Identity</button>
    <button class="btn" onclick={copyAll}>
      {copied ? "✓ Copied All" : "Copy All"}
    </button>
    <div class="spacer"></div>
    <span class="pill pill-ok">
      <span class="pill-dot"></span>
      click any field to copy
    </span>
  </div>

  <div class="disclaimer">
    For testing, development, and form-filling only. All data is randomly
    generated in your browser — no real identities are used or stored.
  </div>

  <ToolFooter />
</div>

<style>
  .identity-card {
    border: 1px solid var(--border-light);
    border-radius: var(--radius-md);
    background: var(--bg-surface);
    overflow: hidden;
    margin-bottom: 16px;
  }

  .field-row {
    display: flex;
    align-items: center;
    padding: 10px 14px;
    gap: 12px;
    border-bottom: 1px solid var(--border-light);
    cursor: pointer;
    transition: background 0.1s;
  }

  .field-row:last-child {
    border-bottom: none;
  }

  .field-row:hover {
    background: var(--accent-bg);
  }

  .field-label {
    font-family: var(--font-mono);
    font-size: 11px;
    color: var(--text-muted);
    width: 80px;
    flex-shrink: 0;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }

  .field-value {
    flex: 1;
    font-family: var(--font-mono);
    font-size: 13px;
    color: var(--text-primary);
  }

  .field-copy {
    font-size: 14px;
    color: var(--text-muted);
    opacity: 0;
    transition: opacity 0.15s;
  }

  .field-row:hover .field-copy {
    opacity: 1;
  }

  .spacer {
    margin-left: auto;
  }

  .disclaimer {
    margin-top: 16px;
    font-family: var(--font-mono);
    font-size: 11px;
    color: var(--text-muted);
    text-align: center;
    padding: 12px;
    background: var(--bg-input);
    border-radius: var(--radius-sm);
  }

  @media (max-width: 640px) {
    .field-label {
      width: 60px;
      font-size: 10px;
    }

    .field-value {
      font-size: 12px;
    }
  }
</style>
