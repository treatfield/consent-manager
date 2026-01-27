# Silktide Consent Manager

A free, lightweight and customizable consent manager/cookie banner for websites, designed to help you comply with GDPR and other privacy regulations.

This version has been reimagined as a **JavaScript Module (ESM)**, making it easy to integrate into modern web applications using tools like Webpack, Vite, or Rollup.

[Learn more](https://silktide.com/consent-manager/) or [Configure it with our wizard](https://silktide.com/consent-manager/install/)

## Features

- **Standard JS Module**: Exported as an ES class for easy integration.
- **Customizable Design**: Easily customize the appearance of the banner and modal.
- **Multiple Positioning Options**: Choose from different positions for the banner and cookie icon.
- **Granular Cookie Control**: Allow users to accept or reject different types of cookies (e.g., essential, analytics, marketing).
- **Event Callbacks**: Trigger custom JavaScript functions when users accept or reject cookies.
- **Accessibility**: Fully accessible with keyboard navigation and ARIA labels.
- **Responsive Design**: Works seamlessly on all devices, from mobile to desktop.

## Installation

You can install the Silktide Consent Manager directly from GitHub using your package manager:

```bash
npm install github:treatfield/consent-manager#v2.0.1
```

## Usage

### 1. Import the Module and CSS

Import the library and its styles into your project:

```javascript
import SilktideCookieBanner from "consent-manager";
import "consent-manager/silktide-consent-manager.css";
```

### 2. Initialization

Initialize the consent manager by creating a new instance of the `SilktideCookieBanner` class with your configuration.

It is recommended to wrap the initialization in a check for `document.body` or use a `DOMContentLoaded` listener to ensure the DOM is ready:

```javascript
const initializeConsentManager = () => {
  const config = {
    cookieTypes: [
        {
          id: 'essential',
          name: 'Essential Cookies',
          description: 'These cookies are necessary for the website to function and cannot be switched off.',
          required: true,
          defaultValue: true,
        },
        {
          id: 'analytics',
          name: 'Analytics Cookies',
          description: 'These cookies help us understand how visitors interact with the website.',
          defaultValue: true,
          onAccept: function() {
            console.log('Analytics cookies accepted');
          },
          onReject: function() {
            console.log('Analytics cookies rejected');
          },
        },
        {
          id: 'marketing',
          name: 'Marketing Cookies',
          description: 'These cookies are used to deliver personalized ads.',
          defaultValue: false,
          onAccept: function() {
            console.log('Marketing cookies accepted');
          },
          onReject: function() {
            console.log('Marketing cookies rejected');
          },
        },
    ],
    text: {
        banner: {
          description: `<p>We use cookies to enhance your experience. By continuing to visit this site, you agree to our use of cookies.</p>`,
          acceptAllButtonText: 'Accept all',
          acceptAllButtonAccessibleLabel: 'Accept all cookies',
          rejectNonEssentialButtonText: 'Reject non-essential',
          rejectNonEssentialButtonAccessibleLabel: 'Reject non-essential',
          preferencesButtonText: 'Preferences',
          preferencesButtonAccessibleLabel: 'Toggle preferences',
        },
        preferences: {
          title: "Customize your cookie preferences",
          description: `<p>We respect your right to privacy. You can choose not to allow some types of cookies. Your cookie preferences will apply across our website.</p>`,
          creditLinkText: 'Get this banner for free',
          creditLinkAccessibleLabel: 'Get this banner for free',
        },
    },
    position: {
        banner: 'bottomRight', // Options: 'bottomRight', 'bottomLeft', 'center', 'bottomCenter'
        cookieIcon: 'bottomLeft', // Options: 'bottomRight', 'bottomLeft'
    },
  new SilktideCookieBanner(config);
};

if (document.readyState === "loading") {
  document.addEventListener("DOMContentLoaded", initializeConsentManager, {
    once: true,
  });
} else {
  initializeConsentManager();
}
```

## Configuration Options

The `SilktideCookieBanner` constructor accepts a configuration object with the following properties:

### `cookieTypes` (Array)

An array of objects defining the different types of cookies. Each object includes:

- `id`: A unique identifier for the cookie type
- `name`: The name of the cookie type (displayed to the user)
- `description`: A description of the cookie type (displayed to the user)
- `required`: Whether the cookie is essential and cannot be rejected
- `defaultValue`: The default state of the cookie (true for accepted, false for rejected)
- `onAccept`: A callback function triggered when the cookie is accepted
- `onReject`: A callback function triggered when the cookie is rejected

### `text`

An object containing text strings used in the banner and preferences modal:

#### `banner`

- `description`: Main text content for the banner
- `acceptAllButtonText`: Text for the accept all button
- `acceptAllButtonAccessibleLabel`: Accessibility label for accept all button
- `rejectNonEssentialButtonText`: Text for reject button
- `rejectNonEssentialButtonAccessibleLabel`: Accessibility label for reject button
- `preferencesButtonText`: Text for preferences button
- `preferencesButtonAccessibleLabel`: Accessibility label for preferences button

#### `preferences`

- `title`: Title text for the preferences modal
- `description`: Description text for the preferences modal
- `creditLinkText`: Text for the credit link
- `creditLinkAccessibleLabel`: Accessibility label for credit link

### `position`

An object defining the position of the banner and cookie icon:

- `banner`: Position of the banner (options: `bottomRight`, `bottomLeft`, `center`, `bottomCenter`)
- `cookieIcon`: Position of the cookie icon (options: `bottomRight`, `bottomLeft`)

### Callbacks

- `onAcceptAll`: A callback function triggered when the user clicks "Accept All"
- `onRejectAll`: A callback function triggered when the user clicks "Reject Non-Essential"

## Advanced Usage: Extending the Class

For more advanced integrations (e.g., synchronizing with a backend, deep GTM/dataLayer customization, or custom DOM manipulation), you can extend the `SilktideCookieBanner` class.

This allows you to override internal methods and tap into the lifecycle of the banner.

```javascript
import SilktideCookieBanner from "consent-manager";

class MyConsentManager extends SilktideCookieBanner {
  constructor(config) {
    // Wrap or modify the config before passing it to super
    const wrappedConfig = {
      ...config,
      onAcceptAll: () => {
        console.log("Custom global logic for Accept All");
        if (config.onAcceptAll) config.onAcceptAll();
      },
    };

    super(wrappedConfig);
  }

  // Override methods to customize internal behavior
  // For example, modify the banner HTML before it is rendered
  getBannerContent() {
    let content = super.getBannerContent();
    // Use DOMParser or regex to modify 'content'
    return content;
  }

  // Override to add custom logic when the modal is toggled
  toggleModal(show) {
    console.log("Modal visibility changed to:", show);
    super.toggleModal(show);
  }
}

const manager = new MyConsentManager(myConfig);
```

## Styling

The consent manager comes with a default set of styles, but you can easily customize them by overriding the CSS variables in your own stylesheets.

The following variables are scoped to `#silktide-wrapper` to prevent them from being overridden by styles coming from the site the consent manager is used on:

```css
#silktide-wrapper {
  --focus: 0 0 0 2px #ffffff, 0 0 0 4px #000000, 0 0 0 6px #ffffff;
  --boxShadow: -5px 5px 10px 0px #00000012, 0px 0px 50px 0px #0000001a;
  --fontFamily: Helvetica Neue, Segoe UI, Arial, sans-serif;
  --primaryColor: #533be2; /* Primary color for buttons and links */
  --backgroundColor: #ffffff; /* Background color for the banner and modal */
  --textColor: #253b48; /* Text color */
  --backdropBackgroundColor: #00000033; /* Backdrop background color */
  --backdropBackgroundBlur: 0px; /* Backdrop blur effect amount */
  --cookieIconColor: #533be2; /* Cookie icon color */
  --cookieIconBackgroundColor: #ffffff; /* Cookie icon background color */
}
```

## License

This project is licensed under the [MIT License](./LICENSE).

## Contributing

Contributions are welcome! Please open an issue or submit a pull request on GitHub.
