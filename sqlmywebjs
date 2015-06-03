$(function () {

    /** ------------------------------------------------------------ **/
    /** --------------- Custom Function's JavaScript --------------- **/
    /** ------------------------------------------------------------ **/

    function replaceAt(s, n, t) { // Remplace le caractère à l'index "n" de la chaine de caractère "s" par la chaine de caractères "t"
        return s.substring(0, n) + t + s.substring(n + 1);
    }

    function insertAt(s, index, string) { // Insère la chaine de caractère "string" à l'index "index" de "s"
        return s.substr(0, index) + string + s.substr(index);
    }

    function matching(testingString, stringRex) { // Retourne tous les string qui match le pattern

        //stringRex = "(" + $.trim(stringRex) + "| (" + $.trim(stringRex) + "\.*[a-zA-Z_0-9]))$"
        stringRex = "(" + stringRex + ")+|[^\\S]*(" + stringRex + "\\.[a-zA-Z0-9\\_]*)+";

        var pattern = new RegExp(stringRex, 'g');
        return testingString.match(pattern);
    }

    function removeFrom(s, string, type) { // Retire la chaine de caractères "string" de "s"

        if (type == "tabs") {

            $myMatch = matching(s, string);

            for ($i = 0; $i < $myMatch.length; $i++) {

                if (s.indexOf($myMatch[$i] + ",") != -1) {

                    s = s.replace($myMatch[$i] + ",", "");
                }
                else {
                    s = s.replace($myMatch[$i], "");
                }
            }

            return s;
            /*if (s.indexOf(string + ", ") != "-1") {

            return s.replace(string + ", ", "");
            }
            return s.replace(string, "");*/
        }
        else if (type == "cols") {

            if (s.indexOf(", " + string) != "-1") {

                return s.replace(", " + string, "");
            }
            if (s.indexOf(string + ",") != "-1") {

                return s.replace(string + ",", "");
            }
            return s.replace(string, "");
        }
    }

    Array.prototype.remove = function (v) { // Remove a value from Array
        this.splice(this.indexOf(v) == -1 ? this.length : this.indexOf(v), 1);
    };

    function getWordPosition(s, wordToFind) { // Permet de trouver la position du mot "wordToFind" dans la chaine "s"
        // Ne fonctionne pas avec les mots-clefs composés comme INNER JOIN, GROUP BY, ORDER BY

        var myStringArray = s.split(" ");
        var myWordPosition = myStringArray.indexOf(wordToFind);

        if (myWordPosition == -1) // Mot pas trouvé
            return -1;
        else
            return myWordPosition;
    }

    function posPreproc(myString, wordToInsert) { // Insère les mots "AND" or "OR" dans la requête

        if (myString == null || myString.length <= 10 || wordToInsert == null || wordToInsert.length < 2)
            return -1;

        if (wordToInsert == "AND") {
            var myOffset = 5;
            var wordToInsertSpace = wordToInsert;
        }
        else if (wordToInsert == "OR") {
            var myOffset = 4;
            var wordToInsertSpace = wordToInsert + " ";
        }

        var myWherePosition = myString.indexOf("WHERE");
        var myGroupByPosition = myString.indexOf("GROUP BY");
        var myOrderByPosition = myString.indexOf("ORDER BY");
        var myParanthesisPosition = myString.indexOf("(");
        /*var myJoinPosition = myString.indexOf("JOIN");
        var myOnPosition = myString.indexOf(" ON ");

        if (wordToInsert == "AND" && myJoinPosition != -1 && myOnPosition != -1 && myOnPosition > myJoinPosition && myWherePosition == -1) {
        // Mot "AND", existance d'un JOIN, d'un ON après le JOIN et pas de WHERE
        if (myString.indexOf("AND", myOnPosition) == -1) {

        console.log("Specific AND");
        myString = insertAt(myString, myOnPosition + 3, " " + wordToInsert + " ");
        }
        }*/

        if (myGroupByPosition == -1) { // "GROUP BY" n'existe pas

            if (myOrderByPosition == -1) { // "ORDER BY" n'existe pas

                if (myParanthesisPosition == -1) { // "Paranthèse" n'existe pas

                    if (wordToInsert != $.trim(myString).split(" ").pop()) { // Si wordToInsert n'est pas le dernier mot

                        myString += " " + wordToInsert;
                    }
                }
                else if (myParanthesisPosition > myWherePosition) { // Pas de ORDER BY, GROUP BY mais Paranthèse après le WHERE
                    // Place wordToInsert avant la paranthèse si pas déjà un wordToInsert

                    if (myString.indexOf(wordToInsert, myParanthesisPosition - myOffset) == -1) {

                        myString = insertAt(myString, myParanthesisPosition - 1, " " + wordToInsert);
                    }
                } // Paranthesis

            }
            else { // "ORDER BY" existe

                if (myParanthesisPosition == -1) { // ORDER BY mais pas de Paranthèses

                    if (myString.indexOf(wordToInsertSpace, myOrderByPosition - myOffset) == -1) {

                        myString = insertAt(myString, myOrderByPosition - 1, " " + wordToInsert);
                    }
                }
                else if (myParanthesisPosition != -1 && myParanthesisPosition > myWherePosition && myParanthesisPosition < myOrderByPosition) { // Pas de GROUP BY mais Paranthèse après le WHERE
                    // et avant le ORDER BY -> Place wordToInsert avant la paranthèse si pas déjà un wordToInsert

                    if (myString.indexOf(wordToInsertSpace, myParanthesisPosition - myOffset) == -1) {

                        myString = insertAt(myString, myParanthesisPosition - 1, " " + wordToInsert);
                    }
                } // Paranthesis
            } // Order By

        }
        else { // "GROUP BY" existe

            if (myParanthesisPosition == -1) { // GROUP BY mais pas de Paranthèses

                if (myString.indexOf(wordToInsertSpace, myGroupByPosition - myOffset) == -1) {

                    myString = insertAt(myString, myGroupByPosition - 1, " " + wordToInsert);
                }
            }
            else if (myParanthesisPosition != -1 && myParanthesisPosition > myWherePosition && myParanthesisPosition < myGroupByPosition) { // Paranthèse après le WHERE
                // et avant le GROUP BY -> Place wordToInsert avant la paranthèse si pas déjà un wordToInsert

                if (myString.indexOf(wordToInsert, myParanthesisPosition - myOffset) == -1) {

                    myString = insertAt(myString, myParanthesisPosition - 1, " " + wordToInsert);
                }
            } // Paranthesis
        } // Group By

        return myString;
    }

    function posPreprocJoin(myString, wordToInsert) { // Insère les mots "JOIN", "INNER JOIN", "OUTER JOIN", "LEFT JOIN", "RIGHT JOIN" dans la requête

        if (myString == null || myString.length <= 10 || wordToInsert == null || wordToInsert.length < 4)
            return -1;

        var myWherePosition = myString.indexOf("WHERE");
        var myFromPosition = myString.indexOf("FROM");

        if (myFromPosition != -1 && myWherePosition != -1 && myWherePosition > myFromPosition) { // Si FROM et WHERE présent et dans le bon ordre

            myString = insertAt(myString, myWherePosition - 1, " " + wordToInsert);
        }
        else if (myFromPosition != -1 && (myFromPosition + 4 >= $.trim(myString).length)) { // Si FROM est le dernier mot

            myString += " " + wordToInsert;
        }

        return myString;
    }

    /** ------------------------------------------------------------ **/
    /** ----------------- Third-Party's JavaScript ----------------- **/
    /** ------------------------------------------------------------ **/

    if (sessionStorage.getItem("userChoiceToast") === null) { // Initialisation de la variable

        sessionStorage.userChoiceToast = "";
    }

    toastr.options = {
        "closeButton": true,
        "debug": false,
        "newestOnTop": true,
        "positionClass": "toast-top-full-width",
        "preventDuplicates": true,
        "showDuration": "300",
        "hideDuration": "700",
        "timeOut": "500000",
        "extendedTimeOut": "500000",
        "showEasing": "swing",
        "hideEasing": "linear",
        "showMethod": "fadeIn",
        "hideMethod": "fadeOut"
    };

    if ($("#server-menu").find(".active").length != 0) {

        if ($("#tabs-menu-niv1").children().length == 0) {
            toastr["warning"]("Vous êtes resté inactif trop longtemps. Cliquer sur <b>Accueil</b> pour pouvoir réutiliser le service ou ouvrir dans un nouvel onglet.");
        }

        if (sessionStorage.userChoiceToast != "cacher") {
            toastr["info"]("Il est possible de glisser/déposer les noms des tables et des colonnes dans la zone de requête.");
        }

        $(".toast-close-button").on("click", function () {

            $userChoice = confirm("Cette information n'apparaitra plus lors de vos prochaines visites.");

            if ($userChoice == true) {
                sessionStorage.userChoiceToast = "cacher";
                toastr["info"]("Votre choix est enregistré.");
            } else {
                toastr["info"]("Vous avez choisi de continuer à recevoir cette information.");
                sessionStorage.userChoiceToast = "montrer";
            }
        });
    }

    /** ------------------------------------------------------------ **/
    /** --------------------- App's JavaScript --------------------- **/
    /** ------------------------------------------------------------ **/

    $myTabsMenu = $("#tabs-menu");
    $mySQL = $("#MainContent_sqlRequete");
    $myTabsMenuStatus = [];
    $myColsMenuStatus = [];
    //$myTabsMenuList = $("#tabs-menu li");
    $myTabsMenuList = $("a[id^=tabsRendered_nameTable]");
    $myColsMenuList = $(".cols-menu-list");
    $lastPos = "";

    /* Menu des Tables - Configuration */
    /*$myTabsMenu.css({ "height": ($("#page-content").height() + "100px"),
    "height": "250px"
    "overflow-y": "scroll"
    });*/

    if (sessionStorage.getItem("serveur") === null) {

        sessionStorage.serveur = "null";
    }

    if (sessionStorage.getItem("tabsStatus") !== null) { // If a déjà choisi table dans le #tabs-menu

        $myTabsMenuStatus = JSON.parse(sessionStorage.tabsStatus);

        if ($myTabsMenuStatus !== null) {
            $.each($myTabsMenuStatus, function (index, element) {

                if ($mySQL.val().indexOf(element) != -1) { // If 'element' existe dans la requête
                    $myTabsMenuList.each(function () {

                        if ($.trim($(this).text()) == element && (sessionStorage.lastServeur == sessionStorage.serveur)) {
                            $(this).parent().addClass("active");
                            $(this).parent().find("ul").css("display", "block"); // Affiche le sous-menu
                        }
                    });
                }
            });
        }
    }

    if (sessionStorage.getItem("colsStatus") !== null) { // If a déjà choisi colonne parmi 'cols-menu-list'

        $myColsMenuStatus = JSON.parse(sessionStorage.colsStatus);

        if ($myColsMenuStatus !== null) {
            $.each($myColsMenuStatus, function (index, element) {

                if ($mySQL.val().indexOf(element) != "-1") { // If 'element' existe dans la requête
                    $myColsMenuList.each(function () {

                        $myChain = $(this).closest(".tabs-menu-list").children("a").text() + "." + $.trim($(this).text());

                        if ($myChain == element && (sessionStorage.lastServeur == sessionStorage.serveur)) {
                            $(this).addClass("active");
                        }
                    });
                }
            });
        }
    }

    /** ------------------------------------------------------------ **/
    /** ----------------- Drag & Drop's JavaScript ----------------- **/
    /** ------------------------------------------------------------ **/

    $myTabsMenuList.on("dragstart", function (e) {

        var myTmpChain = $(this).text();

        e.originalEvent.dataTransfer.setData("text/plain", " " + myTmpChain + " ");
    });

    $myTabsMenuList.on("dragend", function (e) {

        $tmpSQL = $mySQL.val();

        if (($tmpSQL.indexOf("WHERE") + 6) == $tmpSQL.indexOf("1=1")) {

            $tmpSQL = $tmpSQL.replace("1=1", " ");
        }
        $tmpSQL = $tmpSQL.replace(/\s{2,}/g, ' '); // Retire tous les espaces (tabulations, espaces, et autres caractères du même groupe) en trop (doublons, etc.)
        $mySQL.val($tmpSQL);
    });

    $myColsMenuList.on("dragstart", function (e) {

        var myTmpChain = $(this).closest(".tabs-menu-list").children("a").text() + "." + $.trim($(this).text());
        e.originalEvent.dataTransfer.setData("text/plain", " " + myTmpChain + " ");
    });

    $myColsMenuList.on("dragend", function (e) {

        $tmpSQL = $mySQL.val();

        $tmpSQL = $tmpSQL.replace(/\s{2,}/g, ' '); // Retire tous les espaces (tabulations, espaces, et autres caractères du même groupe) en trop (doublons, etc.)
        $mySQL.val($tmpSQL);
    });

    $("#sqlHelp .label").on("dragstart", function (e) {

        e.originalEvent.dataTransfer.setData("text/plain", " " + $(this).text() + " ");
    });

    $("#sqlHelp .label").on("dragend", function (e) {

        $tmpSQL = $mySQL.val();

        $tmpSQL = $tmpSQL.replace(/\s{2,}/g, ' '); // Retire tous les espaces (tabulations, espaces, et autres caractères du même groupe) en trop (doublons, etc.)
        $mySQL.val($tmpSQL);
    });

    /** ------------------------------------------------------------ **/
    /** ------------------ Keys Event's JavaScript ----------------- **/
    /** ------------------------------------------------------------ **/

    $("#tabsSearch").on("keyup", function () { // Scroll en fonction du nom de table saisi

        if ($(this).val().length >= 3) {

            var pos = $("a[id^=tabsRendered_nameTable]:contains(" + $(this).val() + ")").first();

            if ($lastPos == "" || $lastPos.offset().top != pos.offset().top) {

                $lastPos = pos;

                pos = $(pos).offset().top - $("#tabs-menu-niv1").offset().top + $("#tabs-menu-niv1").scrollTop();

                $('#tabs-menu-niv1').animate({ scrollTop: pos }, 1000);
            }
        }
    });

    /** ------------------------------------------------------------ **/
    /** ----------------- Click Event's JavaScript ----------------- **/
    /** ------------------------------------------------------------ **/

    $("#server-menu li").on("click", function () { // Détecter serveur courant

        sessionStorage.lastServeur = sessionStorage.serveur;
        sessionStorage.serveur = $(this).first("a").text();
    });

    $("#MainContent_submitSQL").on("click", function () { // Submit Postback

        sessionStorage.lastServeur = sessionStorage.serveur;
    });

    $("#resetSQL").on("click", function (e) { // Reset Postback

        $mySQL.text("SELECT * FROM WHERE 1=1");
        $("#tabs-menu").find(".active").removeClass("active");
        $("#tabs-menu").find(".cols-menu").attr("style", "");

        $myTabsMenuStatus = [];
        $myColsMenuStatus = [];

        sessionStorage.colsStatus = null;
        sessionStorage.tabsStatus = null;
    });

    $myTabsMenuList.on("click", function () { // Click sur la liste des tables

        $tmpSQL = $mySQL.val();
        $tmpSQL = $tmpSQL.toLowerCase();

        $mySelectPos = $tmpSQL.indexOf("select");
        $mySelectStarPos = $tmpSQL.indexOf("select *");
        $myStarPos = $tmpSQL.indexOf("*");
        $myFromPos = $tmpSQL.indexOf("from");
        $myWherePos = $tmpSQL.indexOf("where");
        $myJoinPos = $tmpSQL.indexOf(" join ");

        if ($mySQL.val().indexOf($.trim($(this).text())) != "-1") { // Si la table se trouve déjà dans la requête, on la retire et on la désactive (remove 'active' class)


            $tmpSQL = removeFrom($mySQL.val(), $.trim($(this).first("a").text()), "tabs");

            $(this).parent().removeClass("active");
            $(this).parent().find("ul").attr("style", "").find("li").each(function () { // cache le sous-menu
                $(this).removeClass("active");
            });

            $myTabsMenuStatus.remove($.trim($(this).first("a").text())); // Remove the clicked element from storage

            sessionStorage.tabsStatus = JSON.stringify($myTabsMenuStatus); // Sauvegarde l'état de la page (les tables qui ont été ajoutées à la requête)

        } else { // La table n'est pas déjà dans la requête

            if ($.trim($mySQL.val()).length > 0) {

                if ($myWherePos - $myFromPos <= 5) { // S'il n'y a rien entre les mot-clefs 'FROM'et 'WHERE' (excepté UN espace)
                    $tmpSQL = insertAt($mySQL.val(), $myFromPos + 4, " " + $.trim($(this).first("a").text()));
                }
                else if ($myJoinPos != -1 && $myJoinPos > $myFromPos && $myJoinPos < $myWherePos) { // Si un JOIN entre FROM et WHERE
                    $tmpSQL = insertAt($mySQL.val(), $myWherePos - 1, " " + $.trim($(this).first("a").text()));
                }
                else {
                    $tmpSQL = insertAt($mySQL.val(), $myFromPos + 4, " " + $.trim($(this).first("a").text()) + ",");
                }
                $(this).parent().addClass("active");
                $(this).parent().find("ul").css("display", "block"); // Affiche le sous-menu

                if ($myTabsMenuStatus === null) {
                    $myTabsMenuStatus = [];
                }

                if ($myTabsMenuStatus.indexOf($.trim($(this).first("a").text())) == -1) {
                    $myTabsMenuStatus.push($.trim($(this).first("a").text())); // Add the clicked element to storage
                }

                sessionStorage.tabsStatus = JSON.stringify($myTabsMenuStatus); // Sauvegarde l'état de la page (les tables qui ont été ajoutées à la requête)
            }
        }

        $tmpSQL = $tmpSQL.replace(/\s{2,}/g, ' '); // Retire tous les espaces (tabulations, espaces, et autres caractères du même groupe) en trop (doublons, etc.)

        if ($tmpSQL.indexOf("FROM") == 7) {
            $tmpSQL = insertAt($tmpSQL, 6, " *");
        }

        $mySQL.val($tmpSQL);
    });

    $myColsMenuList.on("click", function () { // Click sur la liste des tables

        $tmpSQL = $mySQL.val();
        $tmpSQL = $tmpSQL.toLowerCase();

        $mySelectPos = $tmpSQL.indexOf("select");
        $mySelectStarPos = $tmpSQL.indexOf("select *");
        $myStarPos = $tmpSQL.indexOf("*");
        $myFromPos = $tmpSQL.indexOf("from");

        $myChain = $(this).closest(".tabs-menu-list").children("a").text() + "." + $.trim($(this).text());

        if ($mySQL.val().indexOf($myChain) != -1) { // Si la table se trouve déjà dans la requête, on la retire et on la désactive (remove 'active' class)

            $tmpSQL = removeFrom($mySQL.val(), $myChain, "cols");

            $(this).removeClass("active");

            $myColsMenuStatus.remove($myChain); // Remove the clicked element from storage

            sessionStorage.colsStatus = JSON.stringify($myColsMenuStatus); // Sauvegarde l'état de la page (les tables qui ont été ajoutées à la requête)

        } else { // La table n'est pas déjà dans la requête

            if ($.trim($mySQL.val()).length > 0) {

                if ($mySelectStarPos != -1 && $myStarPos != -1) { // S'il y a un 'SELECT *'

                    $tmpSQL = replaceAt($mySQL.val(), $myStarPos, $myChain);
                }
                else {

                    if ($myFromPos - $mySelectPos == 7) { // S'il n'y a rien entre les mot-clefs 'SELECT'et 'FROM' (excepté UN espace)
                        $tmpSQL = insertAt($mySQL.val(), $myFromPos - 1, " " + $myChain);
                    }
                    else {
                        $tmpSQL = insertAt($mySQL.val(), $myFromPos - 1, ", " + $myChain + " ");
                    }
                }
                $(this).closest(".tabs-menu-list").addClass("active").find("ul").css("display", "block"); // Affiche le sous-menu
                $(this).addClass("active");

                if ($myColsMenuStatus === null) {
                    $myColsMenuStatus = [];
                }

                if ($myColsMenuStatus.indexOf($myChain) == -1) {

                    $myColsMenuStatus.push($myChain); // Add the clicked element to storage
                }

                sessionStorage.colsStatus = JSON.stringify($myColsMenuStatus); // Sauvegarde l'état de la page (les colonnes qui ont été ajoutées à la requête)
            }
        }

        $tmpSQL = $tmpSQL.replace(/\s{2,}/g, ' '); // Retire tous les espaces (tabulations, espaces, et autres caractères du même groupe) en trop (doublons, etc.)

        if ($tmpSQL.indexOf("FROM") == 7) {
            $tmpSQL = insertAt($tmpSQL, 6, " *");
        }

        $mySQL.val($tmpSQL);
    });

    $("#sqlHelp .label").on("click", function () { // Rajoute le texte des préprocesseurs à la requête s'il n'existe pas déjà
        $tmpSQL = $mySQL.val();

        var myTester = ["AND", "OR", "JOIN", "INNER JOIN", "LEFT JOIN", "RIGHT JOIN", "ON", "HAVING", "COUNT(*)"];

        if ($tmpSQL.indexOf($(this).text()) == -1 && (myTester.indexOf($(this).text()) == -1)) {

            $tmpSQL = $tmpSQL + " " + $(this).text();
            $tmpSQL = $.trim($tmpSQL).replace(/\s{2,}/g, ' '); // Retire tous les espaces (tabulations, espaces, et autres caractères du même groupe) en trop (doublons, etc.)
            $mySQL.val($tmpSQL);
        }
        else if (myTester.indexOf($(this).text()) != -1) {

            var tmpSwitch = myTester.indexOf($(this).text());

            switch (tmpSwitch) {
                case 0: // AND

                    $tmpSQL = posPreproc($tmpSQL, "AND");
                    break;

                case 1: // OR

                    $tmpSQL = posPreproc($tmpSQL, "OR");
                    break;
                case 2: // JOIN

                    $tmpSQL = posPreprocJoin($tmpSQL, "JOIN");
                    break;
                case 3: // INNER JOIN

                    $tmpSQL = posPreprocJoin($tmpSQL, "INNER JOIN");
                    break;
                case 4: // LEFT JOIN

                    $tmpSQL = posPreprocJoin($tmpSQL, "LEFT JOIN");
                    break;
                case 5: // RIGHT JOIN

                    $tmpSQL = posPreprocJoin($tmpSQL, "RIGHT JOIN");
                    break;
                case 6: // ON

                    var myJoinPosition = $tmpSQL.indexOf("JOIN");
                    var myWherePosition = $tmpSQL.indexOf("WHERE");

                    if (myJoinPosition != -1 && myWherePosition != -1 && myWherePosition > myJoinPosition) { // Si JOIN et WHERE présent et dans le bon ordre

                        if ($tmpSQL.indexOf(" ON ", myWherePosition - 4) == -1) {

                            $tmpSQL = insertAt($tmpSQL, myWherePosition - 1, " " + $(this).text());
                        }
                    }
                    else if (myJoinPosition != -1 && (myJoinPosition + 4 >= $.trim($tmpSQL).length)) { // Si JOIN est le dernier mot

                        $tmpSQL += " " + $(this).text();
                    }
                    break;
                case 7: // HAVING

                    var myGroupByPosition = $tmpSQL.indexOf("GROUP BY");
                    var myOrderByPosition = $tmpSQL.indexOf("ORDER BY");

                    if (myGroupByPosition != -1 && (myGroupByPosition + 8 >= $.trim($tmpSQL).length)) { // SI GROUP BY est le dernier mot

                        $tmpSQL += " " + $(this).text();
                    }
                    else if (myGroupByPosition != -1) {

                        if ($tmpSQL.indexOf(" HAVING ", myGroupByPosition) == -1) {

                            if (myOrderByPosition != -1)
                                $tmpSQL = insertAt($tmpSQL, myOrderByPosition - 1, " " + $(this).text());
                            else
                                $tmpSQL += " " + $(this).text();
                        }
                    }
                    break;
                case 8: // COUNT(*)

                    var mySelectPosition = $tmpSQL.indexOf("SELECT");
                    var mySelectStarPosition = $tmpSQL.indexOf("SELECT *");
                    var myFromPosition = $tmpSQL.indexOf("FROM");
                    var myStarPosition = $tmpSQL.indexOf(" * ");
                    var potentialComa = "";

                    if (mySelectPosition != -1 && myFromPosition != -1 && myFromPosition > mySelectPosition) { // Si SELECT et FROM présent et dans le bon ordre

                        if ($tmpSQL.indexOf(" COUNT(*) ", mySelectPosition) == -1) {

                            if (myFromPosition - mySelectPosition > 9) // Présence d'au moins l'étoile ou plus
                                potentialComa = ", ";
                            $tmpSQL = insertAt($tmpSQL, myFromPosition - 1, " " + potentialComa + $(this).text());
                            $tmpSQL = $tmpSQL.replace(" * ", " ");
                        }
                    }
                    else if ((mySelectPosition != -1 || mySelectStarPosition != -1) && ((mySelectPosition + 6 >= $.trim($tmpSQL).length) || (mySelectStarPosition + 9 >= $.trim($tmpSQL).length))) {
                        // Si SELECT est le dernier mot

                        $tmpSQL += " " + $(this).text();
                        $tmpSQL = $tmpSQL.replace(" * ", " ");
                    }
                    break;
            }

            //$tmpSQL = $tmpSQL + " " + $(this).text();
            $tmpSQL = $.trim($tmpSQL).replace(/\s{2,}/g, ' '); // Retire tous les espaces (tabulations, espaces, et autres caractères du même groupe) en trop (doublons, etc.)
            $mySQL.val($tmpSQL);
        }
    });
});
