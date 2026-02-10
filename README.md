//playwright-structure
├─ tests/
│  ├─ task-board.spec.js
│  ├─ testData.js
│  └─ helpers.js
├─ playwright.config.js
└─ package.json

//Playwright Config
const { defineConfig } = require('@playwright/test');

module.exports = defineConfig({
  testDir: './tests',
  use: {
    baseURL: 'https://animated-gingersnap-8cf7f2.netlify.app/',
    headless: true,
    screenshot: 'only-on-failure',
    trace: 'on-first-retry',
  },
});

//shared Login Helper
const { expect } = require('@playwright/test');

async function login(page) {
  await page.goto('/');

  await page.fill('input[name="username"]', 'admin');
  await page.fill('input[name="password"]', 'password123');
  await page.click('button[type="submit"]');

  // Post-login sanity check
  await expect(page.getByText('Web Application')).toBeVisible();
}

module.exports = { login };

//Test Data Test cases
module.exports = [
  {
    name: 'Test Case 1 - Web App auth task',
    application: 'Web Application',
    task: 'Implement user authentication',
    column: 'To Do',
    tags: ['Feature', 'High Priority'],
  },
  {
    name: 'Test Case 2 - Web App navigation bug',
    application: 'Web Application',
    task: 'Fix navigation bug',
    column: 'To Do',
    tags: ['Bug'],
  },
  {
    name: 'Test Case 3 - Web App design updates',
    application: 'Web Application',
    task: 'Design system updates',
    column: 'In Progress',
    tags: ['Design'],
  },
  {
    name: 'Test Case 4 - Mobile push notifications',
    application: 'Mobile Application',
    task: 'Push notification system',
    column: 'To Do',
    tags: ['Feature'],
  },
  {
    name: 'Test Case 5 - Mobile offline mode',
    application: 'Mobile Application',
    task: 'Offline mode',
    column: 'In Progress',
    tags: ['Feature', 'High Priority'],
  },
  {
    name: 'Test Case 6 - Mobile app icon design',
    application: 'Mobile Application',
    task: 'App icon design',
    column: 'Done',
    tags: ['Design'],
  },
];

//Test Suite
const { test, expect } = require('@playwright/test');
const { login } = require('./helpers');
const testCases = require('./testData');

test.describe('Task Board Validation', () => {
  for (const tc of testCases) {
    test(tc.name, async ({ page }) => {
      // Login
      await login(page);

      // Navigate to application
      await page.click(`text=${tc.application}`);

      // Locate column
      const column = page.locator(`[data-testid="column-${tc.column}"]`);
      await expect(column).toBeVisible();

      // Verify task exists in correct column
      const taskCard = column.locator(`text=${tc.task}`);
      await expect(taskCard).toBeVisible();

      // Verify tags
      for (const tag of tc.tags) {
        await expect(
          taskCard.locator(`text=${tag}`)
        ).toBeVisible();
      }
    });
  }
});

//Run Test
npx playwright test


