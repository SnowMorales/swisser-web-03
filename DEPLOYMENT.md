# Deployment Guide

## GitHub Pages Deployment

### Automatic Deployment (Recommended)

The project is configured with GitHub Actions for automatic deployment to GitHub Pages.

#### Setup Steps:

1. **Enable GitHub Pages in Repository Settings:**
   - Go to Settings → Pages
   - Source: Select "GitHub Actions"

2. **Push to Main Branch:**
   ```bash
   git add .
   git commit -m "Deploy to GitHub Pages"
   git push origin main
   ```

3. **Monitor Deployment:**
   - Check Actions tab for build status
   - Once deployed, site will be available at:
     `https://[username].github.io/[repository-name]/`

### Manual Deployment

If you need to deploy manually:

```bash
# Install gh-pages if not already installed
npm install --save-dev gh-pages

# Build and deploy
npm run deploy
```

### Configuration

#### Base Path
The `vite.config.ts` automatically detects GitHub Actions environment and sets the correct base path:

```typescript
base: process.env.GITHUB_ACTIONS 
  ? `/${process.env.GITHUB_REPOSITORY?.split('/')[1] || ''}/` 
  : '/'
```

#### Custom Domain
To use a custom domain:
1. Create a `CNAME` file in the `public` folder
2. Add your domain (e.g., `example.com`)
3. Configure DNS settings in your domain provider

### Environment Variables

For production secrets:
1. Go to Settings → Secrets and variables → Actions
2. Add secrets prefixed with `VITE_`
3. Update workflow to include them:

```yaml
- name: Build with Vite
  run: npm run build
  env:
    VITE_API_KEY: ${{ secrets.VITE_API_KEY }}
```

### Troubleshooting

#### Build Fails
- Check TypeScript errors: `npm run typecheck`
- Check ESLint: `npm run lint`
- Check build locally: `npm run build`

#### 404 Errors
- Ensure base path is correctly set in `vite.config.ts`
- For React Router, check if using HashRouter for GitHub Pages

#### Assets Not Loading
- Check browser console for path errors
- Verify base path configuration
- Ensure all assets are in `public` or imported properly

### Performance Optimization

The build warns about chunk size. To optimize:

1. **Dynamic Imports for Routes:**
```tsx
const HomePage = lazy(() => import('./pages/HomePage'))
```

2. **Split Vendor Chunks:**
```typescript
// vite.config.ts
build: {
  rollupOptions: {
    output: {
      manualChunks: {
        vendor: ['react', 'react-dom'],
        gsap: ['gsap', '@gsap/react']
      }
    }
  }
}
```

### Workflow Details

The GitHub Actions workflow (`deploy.yml`):
- Triggers on push to main/master
- Uses Node.js 20
- Runs type checking before build
- Caches dependencies for faster builds
- Deploys to GitHub Pages environment

### Local Preview

To preview the production build locally:

```bash
npm run build
npm run preview
```

This will serve the built files at `http://localhost:4173`