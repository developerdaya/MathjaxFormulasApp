
# MathsFormulasApp
```
    <WebView
        android:id="@+id/webView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

```

### 1. **Enable JavaScript in the WebView**

By default, JavaScript is disabled in WebViews. MathJax relies on JavaScript, so you need to explicitly enable it:

```kotlin
binding.webView.settings.javaScriptEnabled = true
```

### 2. **Ensure Visibility is Set Correctly**

You're setting the WebView to be invisible initially and making it visible after the page is loaded. This is fine, but ensure that the `WebView` is being hidden and shown correctly.

### 3. **Handle the Loading Properly**

If you're still not seeing content, try delaying the visibility change or make sure the page load is fully completed before setting the WebView visible.

### Updated Code

Here’s an updated version of your `MainActivity`:

```kotlin
package com.example.exampleapp

import android.os.Bundle
import android.view.View
import android.webkit.WebView
import android.webkit.WebViewClient
import androidx.appcompat.app.AppCompatActivity
import com.example.exampleapp.databinding.ActivityMainBinding
import com.example.exampleapp.util.showToast

class MainActivity : AppCompatActivity() {
    lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        val mathFormulas = """
            <h2>Differentiation Formulas</h2>
            <p>\(\frac{d}{dx} f(x) = f'(x)\)</p>
            <p>\(\frac{d}{dx} (x^n) = nx^{n-1}\)</p>
            
            <h2>Integration Formulas</h2>
            <p>\(\int f'(x)\,dx = f(x) + C\)</p>
            <p>\(\int x^n \, dx = \frac{x^{n+1}}{n+1} + C\)</p>
        """.trimIndent()

        val htmlData = getScriptString(mathFormulas)

        // Enable JavaScript for MathJax
        binding.webView.settings.javaScriptEnabled = true

        // Set WebViewClient to capture when the page is fully loaded
        binding.webView.setWebViewClient(object : WebViewClient() {
            override fun onPageFinished(view: WebView?, url: String?) {
                showToast("Page loaded")
                binding.webView.visibility = View.VISIBLE
            }
        })

        // Initially hide WebView
        binding.webView.visibility = View.INVISIBLE

        // Load the HTML content
        binding.webView.loadDataWithBaseURL(null, htmlData, "text/html", "UTF-8", null)
    }
}

fun getScriptString(text: String): String {
    return """
        <!DOCTYPE html>
        <html lang="en">
        <head>
          <meta charset="UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
          <script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
          <script>
            window.addEventListener('load', function() {
              MathJax.typesetPromise().then(function() {
                document.body.style.visibility = 'visible';
              }).catch(function(err) {
                console.error('MathJax typesetting error:', err);
              });
            });
          </script>
        </head>
        <body style='visibility:hidden;'>
          $text
        </body>
        </html>
    """.trimIndent()
}
```

### Key Changes:
1. **Enabled JavaScript**: Added `binding.webView.settings.javaScriptEnabled = true` to allow JavaScript to run in the WebView, which is required for MathJax.
2. **Maintained the original logic** for loading HTML with MathJax and displaying it once the page is loaded.

### Additional Debugging Steps:

If the above still doesn't work, try these:
- **Check Console Logs**: See if there's an error in the WebView logs using `Logcat`.
- **Simplify HTML**: Start with a very basic `MathJax` example to see if the issue is with the formulas themselves or with the WebView setup.

Let me know if this resolves the issue!
