Stop the rubber-band overscroll bounce on desktop so the app feels native. Set `overscroll-behavior: none` on the root scroller, gated behind a `@media (pointer: fine)` query so touch devices keep their native behavior. Pull-to-refresh on mobile must keep working.

Then check the running app. On desktop, scrolling past the top or bottom of any view shows no bounce and no scroll chaining into whatever is behind it. On a touch viewport, pull-to-refresh and normal scrolling are unchanged. Cover the main views and any nested scroll areas.

Report the rule you added, the views you checked, and any place you kept the default behavior.
