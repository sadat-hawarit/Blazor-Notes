```razor
@inherits LayoutComponentBase
<div class="top-bar">
    <a class="logo" href="">
        <img src="./img/logo.svg" />
    </a>

    <NavLink href="" class="nav-tab" Match="NavLinkMatch.All">
        <img src="./img/pizza-slice.svg" />
        <div>Get Pizza</div>
    </NavLink>
    <NavLink href="myorders" class="nav-tab">
        <img src="./img/bike.svg"/>
        <div>My Orders</div>

    </NavLink>
</div>

<div class="content">
    @Body
</div>
```