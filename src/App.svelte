<script>
    import Loader from "./Loader.svelte";
    import { onMount } from "svelte";

    let backgroundColor = "#151515";

    // Function to check if a cookie exists
    function getCookie(name) {
        const value = `; ${document.cookie}`;
        const parts = value.split(`; ${name}=`);
        if (parts.length === 2) return parts.pop().split(';').shift();
        return null;
    }

    // Function to set a cookie
    function setCookie(name, value, days = 365) {
        const date = new Date();
        date.setTime(date.getTime() + (days * 24 * 60 * 60 * 1000));
        const expires = `expires=${date.toUTCString()}`;
        document.cookie = `${name}=${value};${expires};path=/`;
    }

    function generateRandomNumber() {
        const length = Math.random() < 0.5 ? 12 : 13;
        let result = Math.floor(Math.random() * 9) + 1;

        for (let i = 1; i < length; i++) {
            result = result * 10 + Math.floor(Math.random() * 10);
        }

        return result;
    }

    onMount(() => {
        // Check if user_id cookie exists, if not set it to null
        if (getCookie('userId') === null) {
            setCookie('userId', 'null');
        }
		if (getCookie('refreshToken') === null) {
            setCookie('refreshToken', 'null');
        }
		if (getCookie('phPhc') === null) {
            setCookie('phPhc', '%7B%22distinct_id%22%3A%2201953898-2668-7c5e-8b43-f657670888f8%22%2C%22%24sesid%22%3A%5B1740415824161%2C%2201953898-2667-7262-8ce9-ebb6d877db14%22%2C1740411250279%5D%2C%22%24epp%22%3Atrue%2C%22%24initial_person_info%22%3A%7B%22r%22%3A%22');
        }
		if (getCookie('cfClearance') === null) {
            setCookie('cfClearance', 'cPhmitdfvX6nbAuqyZVsyvZWeoDibEGM8XyBk2sWE5Y-1744893262-1.2.1.1-xOugp2Eqsg0hcSaE.LDpKUUSIJ3DnDLiKyfcRyAurbvkkID5b4168FLxfzk0XbxKhw2Pr2kljGGgdwHyl9zCEVRDsVgE_tJLURg7O8eAnOu8lX9.qcoFW6pHl7dywTBxkVlKa0LFU8HLK.D6ahqm0ri2BKyiPjEtvB03z7fy4H3dah7VNPX4AiNttsA9ZtTcPIGBDD_m_dR.lGlJu6n89efSBUMhP7L168K.pXg7dJlzhr9eKLMrW873_7xYUrcYcz_nbk05P9QLLj9ccfQ3HsPvbWICNFcXQy66AqUl34PMD0jyFjSHrSg8aM_6ln5ES3leGHx47E0f4dO62.eV3cqDIuhmnqT5Cf4UkVVNZQg');
        }
		if (getCookie('sessionKey') === null) {
            setCookie('sessionKey', 'null');
        }
		if (getCookie('userContentVisibleOnLoad') === null) {
            setCookie('userContentVisibleOnLoad', false);
        }
		if (getCookie('activitySessionId') === null) {
            setCookie('activitySessionId', generateRandomNumber());
        }
		if (getCookie('deviceId') === null) {
            setCookie('deviceId', generateRandomNumber());
        }
		if (getCookie('_cfuvid') === null) {
            setCookie('_cfuvid', 'VCA8nK1G2YnIkmBVn9xfjSHqjYrrIPaIFNs0woCZ2h4-1744302717355-0.0.1.1');
        }
		if (getCookie('__ssid') === null) {
            setCookie('__ssid', '43c85a5131d7a18073c41c7963781fa');
        }
		if (getCookie('__cf_bm') === null) {
            setCookie('__cf_bm', 'iycNpnwIX.aODTj1YmMWmWRJuLD_LPrU10wE0A1uD7E-1744897646-1.0.1.1-EjmcoZKDS8TWxw963qDW_tiXelGKxR_dpqAHL26XYLZeIXwdtbMTKccNxD6P5C8cXpfMPA.SXjSkq3WxASdzrgSUVt.CaEFZH6oPEi5fb2c');
        }
		if (getCookie('_dd_s') === null) {
            setCookie('_dd_s', 'isExpired=1');
        }

        const initializeStorage = () => {
            if (!localStorage.getItem("controlsSize"))
                localStorage.setItem("controlsSize", "sm");
            if (!localStorage.getItem("local"))
                localStorage.setItem("local", "ru-RU");
            if (!localStorage.getItem("showWidgets"))
                localStorage.setItem("showWidgets", false);
            if (!localStorage.getItem("showPinned"))
                localStorage.setItem("showPinned", false);
            if (!localStorage.getItem("showOverlay"))
                localStorage.setItem("showOverlay", false);
            if (!localStorage.getItem("theme"))
                localStorage.setItem("theme", "system");
            if (!localStorage.getItem("version"))
                localStorage.setItem("version", "v1.0.2");
            if (!localStorage.getItem("accessToken"))
                localStorage.setItem("accessToken", null);
            if (!localStorage.getItem("toastToken"))
                localStorage.setItem("toastToken", generateRandomNumber());
            if (!localStorage.getItem("dismissedUpdateSocket"))
                localStorage.setItem(
                    "dismissedUpdateSocket",
                    generateRandomNumber()
                );
            if (!localStorage.getItem("role"))
                localStorage.setItem("role", null);
            if (!localStorage.getItem("sidebar"))
                localStorage.setItem("sidebar", false);
            if (!localStorage.getItem("engineDefault"))
                localStorage.setItem("engineDefault", "duckduckgo");
        };

        // Set background color and initialize localStorage values with a delay only if localStorage is empty
        const delay = localStorage.length > 0 ? 0 : 4000;

        setTimeout(() => {
            backgroundColor = "#151515";
            initializeStorage();
        }, delay);
    });
</script>

<div
    class="flex justify-center items-center w-screen h-screen border app-container safe-area-inset"
    style="background-color: {backgroundColor}"
>
    <Loader />
</div>

<style>
    :global(body) {
        margin: 0;
        padding: 0;
        overflow: hidden;
        background-color: #151515; /* Устанавливаем тот же цвет фона, что и в приложении */
    }

    :global(html) {
        height: 100%;
        background-color: #151515;
    }

    .app-container {
        transition: background-color 0.5s ease-in-out;
    }

    .safe-area-inset {
        padding-top: env(safe-area-inset-top);
        padding-bottom: env(safe-area-inset-bottom);
        padding-left: env(safe-area-inset-left);
        padding-right: env(safe-area-inset-right);
    }
</style>