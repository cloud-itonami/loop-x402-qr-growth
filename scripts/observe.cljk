(ns observe (:require ["fs" :as fs] ["crypto" :as crypto]))
(def url "https://x402.nexus/qr/capabilities")
(defn save! [r]
  (.mkdirSync fs "reports" #js {:recursive true})
  (.writeFileSync fs "reports/latest-observation.edn" (str (pr-str r) "\n"))
  (println (pr-str (dissoc r :body))))
(-> (js/fetch url #js {:signal (.timeout js/AbortSignal 15000) :redirect "error"})
    (.then (fn [r] (-> (.text r) (.then (fn [body]
      (save! {:observed-at (.toISOString (js/Date.)) :url url :http-status (.-status r)
              :reachable? (.-ok r) :body body
              :sha256 (.digest (.update (.createHash crypto "sha256") body) "hex")
              :physical-issuance :unmeasured :external-demand :unmeasured})
      (when-not (.-ok r) (set! (.-exitCode js/process) 2)))))))
    (.catch (fn [e] (save! {:observed-at (.toISOString (js/Date.)) :url url :reachable? false :error (.-message e)})
              (set! (.-exitCode js/process) 2))))
