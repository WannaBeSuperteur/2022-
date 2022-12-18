## 코드
```kotlin
import kotlin.test.*

data class Influencer (
    var name: String = "",
    var followers: Int = 0
)

fun main(args: Array<String>) {

    val influencers = listOf<Influencer> (
        Influencer("Black Mamba", 2020000),
        Influencer("Koowick Service", 372000),
        Influencer("Kalm Man", 1980000),
        Influencer("Just Two Minutes", 941000),
        Influencer("LOZZY", 11170000)
    )

    // associate
    val test0 = influencers.associate { it.name to it.followers }
    println("associate             -> ${test0}")
    
    // associateBy (with equivalent associate)
    val test1 = influencers.associateBy { it.name }
    val test2 = influencers.associate { it.name to it }
    
    val test3 = influencers.associateBy { it.followers }
    val test4 = influencers.associate { it.followers to it }
    
    println("\nassociateBy 1         -> ${test1}")
    println("associate             -> ${test2}")
    println("\nassociateBy 2         -> ${test3}")
    println("associate             -> ${test4}")
    
    // associateByTo (with equivalent associate)
    val getButton : ((Int) -> Char) = { fols ->
        when (fols) {
            in 10000000..99999999 -> 'D'
            in 1000000..9999999 -> 'G'
            in 100000..999999 -> 'S'
            else -> 'X'
        }
    }
    
    val test5_map = mutableMapOf<String, Influencer>()
    val test5 = influencers.associateByTo (test5_map) { "${it.followers / 1000}K" }
    val test6 = influencers.associate { "${it.followers / 1000}K" to it }
    
    val test7_map = mutableMapOf<Char, Influencer>()
    val test7 = influencers.associateByTo (test7_map) { getButton(it.followers) }
    val test8 = influencers.associate { getButton(it.followers) to it }
    
    println("\nassociateByTo 1       -> ${test5}")
    println("associateByTo 1 map   -> ${test5_map}")
    println("associate             -> ${test6}")
    println("\nassociateByTo 2       -> ${test7}")
    println("associateByTo 2 map   -> ${test7_map}")
    println("associate             -> ${test8}")
    
    // associateTo
    val test9_map = mutableMapOf<String, Int>()
    val test9 = influencers.associateTo (test9_map) { it.name to it.followers }
    
    println("\nassociateTo           -> ${test9}")
    println("associateTo map       -> ${test9_map}")
    
    // associateWith (with equivalent associate)
    val test10 = influencers.associateWith { it.name.length }
    val test11 = influencers.associate { it to it.name.length }
    
    val test12 = influencers.associateWith { getButton(it.followers) }
    val test13 = influencers.associate { it to getButton(it.followers) }
    
    println("\nassociateWith 1       -> ${test10}")
    println("associate             -> ${test11}")
    println("\nassociateWith 2       -> ${test12}")
    println("associate             -> ${test13}")
    
    // associateWithTo (with equivalent associate)
    val test14_map = mutableMapOf<Influencer, String>()
    val test14 = influencers.associateWithTo (test14_map) { "${it.followers / 1000}K" }
    val test15 = influencers.associate { it to "${it.followers / 1000}K" }
    
    val test16_map = mutableMapOf<Influencer, String>()
    val test16 = influencers.associateWithTo (test16_map) { "${it.name}'s fols : ${it.followers}" }
    val test17 = influencers.associate { it to "${it.name}'s fols : ${it.followers}" }
    
    println("\nassociateWithTo 1     -> ${test14}")
    println("associateWithTo 1 map -> ${test14_map}")
    println("associate             -> ${test15}")
    println("\nassociateByTo 2       -> ${test16}")
    println("associateWithTo 2 map -> ${test16_map}")
    println("associate             -> ${test17}")
}
```

## 실행 결과
```kotlin
associate             -> {Black Mamba=2020000, Koowick Service=372000, Kalm Man=1980000, Just Two Minutes=941000, LOZZY=11170000}

associateBy 1         -> {Black Mamba=Influencer(name=Black Mamba, followers=2020000), Koowick Service=Influencer(name=Koowick Service, followers=372000), Kalm Man=Influencer(name=Kalm Man, followers=1980000), Just Two Minutes=Influencer(name=Just Two Minutes, followers=941000), LOZZY=Influencer(name=LOZZY, followers=11170000)}
associate             -> {Black Mamba=Influencer(name=Black Mamba, followers=2020000), Koowick Service=Influencer(name=Koowick Service, followers=372000), Kalm Man=Influencer(name=Kalm Man, followers=1980000), Just Two Minutes=Influencer(name=Just Two Minutes, followers=941000), LOZZY=Influencer(name=LOZZY, followers=11170000)}

associateBy 2         -> {2020000=Influencer(name=Black Mamba, followers=2020000), 372000=Influencer(name=Koowick Service, followers=372000), 1980000=Influencer(name=Kalm Man, followers=1980000), 941000=Influencer(name=Just Two Minutes, followers=941000), 11170000=Influencer(name=LOZZY, followers=11170000)}
associate             -> {2020000=Influencer(name=Black Mamba, followers=2020000), 372000=Influencer(name=Koowick Service, followers=372000), 1980000=Influencer(name=Kalm Man, followers=1980000), 941000=Influencer(name=Just Two Minutes, followers=941000), 11170000=Influencer(name=LOZZY, followers=11170000)}

associateByTo 1       -> {2020K=Influencer(name=Black Mamba, followers=2020000), 372K=Influencer(name=Koowick Service, followers=372000), 1980K=Influencer(name=Kalm Man, followers=1980000), 941K=Influencer(name=Just Two Minutes, followers=941000), 11170K=Influencer(name=LOZZY, followers=11170000)}
associateByTo 1 map   -> {2020K=Influencer(name=Black Mamba, followers=2020000), 372K=Influencer(name=Koowick Service, followers=372000), 1980K=Influencer(name=Kalm Man, followers=1980000), 941K=Influencer(name=Just Two Minutes, followers=941000), 11170K=Influencer(name=LOZZY, followers=11170000)}
associate             -> {2020K=Influencer(name=Black Mamba, followers=2020000), 372K=Influencer(name=Koowick Service, followers=372000), 1980K=Influencer(name=Kalm Man, followers=1980000), 941K=Influencer(name=Just Two Minutes, followers=941000), 11170K=Influencer(name=LOZZY, followers=11170000)}

associateByTo 2       -> {G=Influencer(name=Kalm Man, followers=1980000), S=Influencer(name=Just Two Minutes, followers=941000), D=Influencer(name=LOZZY, followers=11170000)}
associateByTo 2 map   -> {G=Influencer(name=Kalm Man, followers=1980000), S=Influencer(name=Just Two Minutes, followers=941000), D=Influencer(name=LOZZY, followers=11170000)}
associate             -> {G=Influencer(name=Kalm Man, followers=1980000), S=Influencer(name=Just Two Minutes, followers=941000), D=Influencer(name=LOZZY, followers=11170000)}

associateTo           -> {Black Mamba=2020000, Koowick Service=372000, Kalm Man=1980000, Just Two Minutes=941000, LOZZY=11170000}
associateTo map       -> {Black Mamba=2020000, Koowick Service=372000, Kalm Man=1980000, Just Two Minutes=941000, LOZZY=11170000}

associateWith 1       -> {Influencer(name=Black Mamba, followers=2020000)=11, Influencer(name=Koowick Service, followers=372000)=15, Influencer(name=Kalm Man, followers=1980000)=8, Influencer(name=Just Two Minutes, followers=941000)=16, Influencer(name=LOZZY, followers=11170000)=5}
associate             -> {Influencer(name=Black Mamba, followers=2020000)=11, Influencer(name=Koowick Service, followers=372000)=15, Influencer(name=Kalm Man, followers=1980000)=8, Influencer(name=Just Two Minutes, followers=941000)=16, Influencer(name=LOZZY, followers=11170000)=5}

associateWith 2       -> {Influencer(name=Black Mamba, followers=2020000)=G, Influencer(name=Koowick Service, followers=372000)=S, Influencer(name=Kalm Man, followers=1980000)=G, Influencer(name=Just Two Minutes, followers=941000)=S, Influencer(name=LOZZY, followers=11170000)=D}
associate             -> {Influencer(name=Black Mamba, followers=2020000)=G, Influencer(name=Koowick Service, followers=372000)=S, Influencer(name=Kalm Man, followers=1980000)=G, Influencer(name=Just Two Minutes, followers=941000)=S, Influencer(name=LOZZY, followers=11170000)=D}

associateWithTo 1     -> {Influencer(name=Black Mamba, followers=2020000)=2020K, Influencer(name=Koowick Service, followers=372000)=372K, Influencer(name=Kalm Man, followers=1980000)=1980K, Influencer(name=Just Two Minutes, followers=941000)=941K, Influencer(name=LOZZY, followers=11170000)=11170K}
associateWithTo 1 map -> {Influencer(name=Black Mamba, followers=2020000)=2020K, Influencer(name=Koowick Service, followers=372000)=372K, Influencer(name=Kalm Man, followers=1980000)=1980K, Influencer(name=Just Two Minutes, followers=941000)=941K, Influencer(name=LOZZY, followers=11170000)=11170K}
associate             -> {Influencer(name=Black Mamba, followers=2020000)=2020K, Influencer(name=Koowick Service, followers=372000)=372K, Influencer(name=Kalm Man, followers=1980000)=1980K, Influencer(name=Just Two Minutes, followers=941000)=941K, Influencer(name=LOZZY, followers=11170000)=11170K}

associateByTo 2       -> {Influencer(name=Black Mamba, followers=2020000)=Black Mamba's fols : 2020000, Influencer(name=Koowick Service, followers=372000)=Koowick Service's fols : 372000, Influencer(name=Kalm Man, followers=1980000)=Kalm Man's fols : 1980000, Influencer(name=Just Two Minutes, followers=941000)=Just Two Minutes's fols : 941000, Influencer(name=LOZZY, followers=11170000)=LOZZY's fols : 11170000}
associateWithTo 2 map -> {Influencer(name=Black Mamba, followers=2020000)=Black Mamba's fols : 2020000, Influencer(name=Koowick Service, followers=372000)=Koowick Service's fols : 372000, Influencer(name=Kalm Man, followers=1980000)=Kalm Man's fols : 1980000, Influencer(name=Just Two Minutes, followers=941000)=Just Two Minutes's fols : 941000, Influencer(name=LOZZY, followers=11170000)=LOZZY's fols : 11170000}
associate             -> {Influencer(name=Black Mamba, followers=2020000)=Black Mamba's fols : 2020000, Influencer(name=Koowick Service, followers=372000)=Koowick Service's fols : 372000, Influencer(name=Kalm Man, followers=1980000)=Kalm Man's fols : 1980000, Influencer(name=Just Two Minutes, followers=941000)=Just Two Minutes's fols : 941000, Influencer(name=LOZZY, followers=11170000)=LOZZY's fols : 11170000}
```
