<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ebced Değeri Hesaplama ve Esma Bulma</title>
    <style>
        body {
            font-family: Arial, sans-serif;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            padding: 20px;
            text-align: center;
        }
        input, button {
            padding: 10px;
            margin: 5px;
        }
        ul {
            list-style-type: none;
            padding: 0;
        }
        li {
            padding: 5px;
            font-size: 18px;
        }
        h2 {
            font-size: 24px;
        }
        h3 {
            font-size: 20px;
        }
        #details {
            font-size: 20px;
        }
        #result {
            font-size: 22px;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Türkçe İsimlerin Ebced Değerini Hesaplama</h1>
        <input type="text" id="nameInput" placeholder="Türkçe isim girin">
        <button onclick="calculateEbced()">Hesapla</button>
        <h2>Arapça Karşılığı: <span id="arabicName"></span></h2>
        <h2>Ebced Değeri: <span id="ebcedValue"></span></h2>
        <div id="details"></div>
        <div id="result"></div>
        <h2>En Yakın Esma: <span id="closestEsma"></span></h2>
        <h2>Esma'nın Türkçesi: <span id="esmaTurkish"></span></h2>
        <h2>Esma'nın Ebced Değeri: <span id="esmaEbcedValue"></span></h2>
    </div>

    <script>
        const ebcedTable = {
            'ا': 1, 'ب': 2, 'ت': 400, 'ث': 500, 'ج': 3, 'ح': 8, 'خ': 600,
            'د': 4, 'ذ': 700, 'ر': 200, 'ز': 7, 'س': 60, 'ش': 300, 'ص': 90,
            'ض': 800, 'ط': 9, 'ظ': 900, 'ع': 70, 'غ': 1000, 'ف': 80, 'ق': 100,
            'ك': 20, 'ل': 30, 'م': 40, 'ن': 50, 'ه': 5, 'و': 6, 'ي': 10
        };

        const charMap = {
            'a': 'ا', 'b': 'ب', 'c': 'ج', 'ç': 'ج', 'd': 'د', 'e': 'ا', 'f': 'ف',
            'g': 'ك', 'ğ': 'غ', 'h': 'ه', 'ı': 'ا', 'i': 'ي', 'j': 'ج', 'k': 'ك',
            'l': 'ل', 'm': 'م', 'n': 'ن', 'o': 'و', 'ö': 'و', 'p': 'ب', 'r': 'ر',
            's': 'س', 'ş': 'ش', 't': 'ت', 'u': 'و', 'ü': 'و', 'v': 'و', 'y': 'ي',
            'z': 'ز'
        };

        const esmaEbced = {
            'الرحمن': { ebced: 298, turkish: 'ER RAHMAN' }, 
            'الرحيم': { ebced: 258, turkish: 'ER RAHİM' },
            'الملك': { ebced: 90, turkish: 'EL MELİK' },
            'القدوس': { ebced: 170, turkish: 'EL KUDDÜS' },
            'السلام': { ebced: 131, turkish: 'ES SELAM' },
            'المؤمن': { ebced: 137, turkish: 'EL MÜ\'MİN' },
            'المهيمن': { ebced: 145, turkish: 'EL MÜHEYMİN' },
            'العزيز': { ebced: 94, turkish: 'EL AZİZ' },
            'الجبار': { ebced: 206, turkish: 'EL CEBBAR' },
            'المتكبر': { ebced: 662, turkish: 'EL MÜTEKEBBİR' },
            'الخالق': { ebced: 731, turkish: 'EL HALİK' },
            'البارئ': { ebced: 213, turkish: 'EL BARİ' },
            'المصور': { ebced: 336, turkish: 'EL MUSAVVİR' },
            'الغفار': { ebced: 1286, turkish: 'EL GAFFUR' },
            'القهار': { ebced: 306, turkish: 'EL KAHHAR' },
            'الوهاب': { ebced: 14, turkish: 'EL VEHHAB' },
            'الرزاق': { ebced: 308, turkish: 'ER REZZAK' },
            'الفتاح': { ebced: 489, turkish: 'EL FETTAH' },
            'العليم': { ebced: 150, turkish: 'EL ALİM' },
            'القابض': { ebced: 903, turkish: 'EL KABID' },
            'الباسط': { ebced: 72, turkish: 'EL BASIT' },
            'الخافض': { ebced: 1481, turkish: 'EL HAFID' },
            'الرافع': { ebced: 351, turkish: 'ER RAFİ' },
            'المعز': { ebced: 117, turkish: 'EL MUİZZ' },
            'المذل': { ebced: 770, turkish: 'EL MÜZİLL' },
            'السميع': { ebced: 180, turkish: 'ES SEMİ' },
            'البصير': { ebced: 302, turkish: 'EL BASİR' },
            'الحكم': { ebced: 68, turkish: 'EL HAKEM' },
            'العدل': { ebced: 104, turkish: 'EL ADL' },
            'اللطيف': { ebced: 129, turkish: 'EL LATİF' },
            'الخبير': { ebced: 812, turkish: 'EL HABİR' },
            'الحليم': { ebced: 88, turkish: 'EL HALİM' },
            'العظيم': { ebced: 1020, turkish: 'EL AZİM' },
            'الشكر': { ebced: 526, turkish: 'EŞ ŞEKÜR' },
            'العلي': { ebced: 110, turkish: 'EL ALİYY' },
            'الكبير': { ebced: 232, turkish: 'EL KEBİR' },
            'الحفيظ': { ebced: 998, turkish: 'EL HAFİZ' },
            'المقيت': { ebced: 550, turkish: 'EL MUKİT' },
            'الحسيب': { ebced: 80, turkish: 'EL HASİB' },
            'الجليل': { ebced: 73, turkish: 'EL CELİL' },
            'الكريم': { ebced: 270, turkish: 'EL KERİM' },
            'الرقيب': { ebced: 312, turkish: 'EL RAKİB' },
            'المجيب': { ebced: 55, turkish: 'EL MÜCİB' },
            'الواسع': { ebced: 137, turkish: 'EL VASİ' },
            'الحكيم': { ebced: 78, turkish: 'EL HAKİM' },
            'الودود': { ebced: 20, turkish: 'EL VEDUD' },
            'المجيد': { ebced: 57, turkish: 'EL MECİD' },
            'الباعث': { ebced: 573, turkish: 'EL BÂİS' },
            'الشهيد': { ebced: 319, turkish: 'EŞ ŞEHİD' },
            'الحق': { ebced: 108, turkish: 'EL HAKK' },
            'الوكيل': { ebced: 66, turkish: 'EL VEKİL' },
            'القوي': { ebced: 116, turkish: 'EL KAVİ' },
            'المتين': { ebced: 500, turkish: 'EL METİN' },
            'الولي': { ebced: 46, turkish: 'EL VELİ' },
            'الحميد': { ebced: 62, turkish: 'EL HAMİD' },
            'المحصي': { ebced: 148, turkish: 'EL MUHSİ' },
            'المبدئ': { ebced: 572, turkish: 'EL MÜBDİ' },
            'المعيد': { ebced: 124, turkish: 'EL MUÎD' },
            'المحيي': { ebced: 58, turkish: 'EL MUHYİ' },
            'المميت': { ebced: 490, turkish: 'EL MÜMİT' },
            'الحي': { ebced: 18, turkish: 'EL HAYY' },
            'القيوم': { ebced: 156, turkish: 'EL KAYYUM' },
            'الواجد': { ebced: 14, turkish: 'EL VACİD' },
            'الماجد': { ebced: 48, turkish: 'EL MACİD' },
            'الواحد': { ebced: 19, turkish: 'EL VAHİD' },
            'الاحد': { ebced: 13, turkish: 'EL EHAD' },
            'الصمد': { ebced: 134, turkish: 'ES SAMED' },
            'القادر': { ebced: 305, turkish: 'EL KADİR' },
            'المقتدر': { ebced: 744, turkish: 'EL MUKTEDİR' },
            'المقدم': { ebced: 184, turkish: 'EL MUKADDİM' },
            'المؤخر': { ebced: 846, turkish: 'EL MUAHHİR' },
            'الاول': { ebced: 37, turkish: 'EL EVVEL' },
            'الاخر': { ebced: 801, turkish: 'EL ÂHİR' },
            'الظاهر': { ebced: 1106, turkish: 'EZ ZÂHİR' },
            'الباطن': { ebced: 62, turkish: 'EL BATIN' },
            'الوالي': { ebced: 47, turkish: 'EL VALİ' },
            'المتعالي': { ebced: 551, turkish: 'EL MÜTEALİ' },
            'البر': { ebced: 202, turkish: 'EL BERR' },
            'التواب': { ebced: 409, turkish: 'ET TEVVÂB' },
            'المنتقم': { ebced: 630, turkish: 'EL MÜNTEKİM' },
            'العفو': { ebced: 156, turkish: 'EL AFÜV' },
            'الرؤوف': { ebced: 287, turkish: 'ER RAUF' },
            'مالك الملك': { ebced: 1124, turkish: 'MÂLİKÜL MÜLK' },
            'ذو الجلال و الإكرام': { ebced: 1250, turkish: 'ZÜL CELALİ VEL İKRÂM' },
            'المقسط': { ebced: 209, turkish: 'EL MUKSİT' },
            'الجامع': { ebced: 114, turkish: 'EL CAMİ' },
            'الغني': { ebced: 1060, turkish: 'EL GANİ' },
            'المغني': { ebced: 1100, turkish: 'EL MUĞNİ' },
            'المانع': { ebced: 161, turkish: 'EL MANİ' },
            'الضار': { ebced: 1001, turkish: 'ED DÂRR' },
            'النافع': { ebced: 201, turkish: 'EN NÂFİ' },
            'النور': { ebced: 256, turkish: 'EN NUR' },
            'الهادي': { ebced: 20, turkish: 'EL HADİ' },
            'البديع': { ebced: 86, turkish: 'EL BEDİ' },
            'الباقي': { ebced: 113, turkish: 'EL BAKİ' },
            'الوارث': { ebced: 707, turkish: 'EL VÂRİS' },
            'الرشيد': { ebced: 514, turkish: 'ER REŞİD' },
            'الصبور': { ebced: 298, turkish: 'ES SABUR' }
        };

        function calculateEbced() {
            const name = document.getElementById('nameInput').value.trim().toLowerCase();
            const arabicName = name.split('').map(char => charMap[char] || '').join('');
            document.getElementById('arabicName').innerText = arabicName;

            let ebcedValue = 0;
            for (let char of arabicName) {
                ebcedValue += ebcedTable[char] || 0;
            }
            document.getElementById('ebcedValue').innerText = ebcedValue;

            const closestEsma = findClosestEsma(ebcedValue);
            if (closestEsma) {
                document.getElementById('closestEsma').innerText = closestEsma;
                document.getElementById('esmaTurkish').innerText = esmaEbced[closestEsma].turkish;
                document.getElementById('esmaEbcedValue').innerText = esmaEbced[closestEsma].ebced;
            } else {
                document.getElementById('closestEsma').innerText = 'Bulunamadı';
                document.getElementById('esmaTurkish').innerText = '';
                document.getElementById('esmaEbcedValue').innerText = '';
            }

            const detailsHtml = generateDetailsHtml(arabicName);
            document.getElementById('details').innerHTML = detailsHtml;
        }

        function findClosestEsma(ebcedValue) {
            let closestEsma = null;
            let smallestDifference = Infinity;

            for (const esma in esmaEbced) {
                const difference = Math.abs(ebcedValue - esmaEbced[esma].ebced);
                if (difference < smallestDifference) {
                    smallestDifference = difference;
                    closestEsma = esma;
                }
            }

            return closestEsma;
        }

        function generateDetailsHtml(arabicName) {
            let detailsHtml = '<h3>Harflerin Ebced Değerleri</h3><ul>';
            for (let char of arabicName) {
                const ebcedValue = ebcedTable[char] || 0;
                detailsHtml += `<li>${char}: ${ebcedValue}</li>`;
            }
            detailsHtml += '</ul>';
            return detailsHtml;
        }
    </script>
</body>
</html>
