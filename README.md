# tsw
----------------------------------------------------------------------------------------------------------------------------------------
	private AutoGuma AG;
	
	@BeforeAll
	public static void proveriOperativniSistem() {
		assertTrue(System.getProperty("os.name").contains("Windows"));
	}
	
	@BeforeEach
	void init() {
		AG = new AutoGuma("Michelin", true, 18, 180, 40);
	}
-----------------------------------------------------------------------------------------------------------------------------------------
	@Rule
	public final TestName name = new TestName();
	
	assertEquals("getZimskaTest", name.getMethodName());
-----------------------------------------------------------------------------------------------------------------------------------------
@Timeout(value = 5, unit = TimeUnit.SECONDS)

@RunWith(Suite.class)
@SuiteClasses({ AutoGumaTest.class })
//////////////////////////////////////////

@RunWith(JUnitPlatform.class)
@SelectClasses(RacunarTest.class)
////////////////////////////////////////

@RunWith(Suite.class)
@SuiteClasses({ VulkanizerskaRadnjaDodajGumuParametrizedTest.class,
		VulkanizerskaRadnjaPronadjiGumuParazmetrizedTest.class })
////////////////////////////////////////

@RunWith(JUnitPlatform.class)
@SelectClasses({VulkanizerskaRadnjaDodajGumuParameterizedTest.class, VulkanizerskaRadnjaPronadjiGumuParametrizedTest.class})
///////////////////////////////////////

-------------------------------------------------------------------------------------------------------------------------------------------------------

RUNER KLASA

class TestRunner {

	// u okviru ove klase se vrse testovi za prethodno testirane 2 napravljene kolekcije
	public static void main(String [] args) {
		
		Result result =JUnitCore.runClasses(AutoGumaTests.class, VRTests.class);
		
		Logger l = Logger.getLogger(TestRunner.class.toString());
		
		for(Failure f : result.getFailures()) {
			l.warning(f.toString());
		}
		
		l.info("Vreme izvrsavanja " +result.getRunTime());
		l.info("Ukupan broj testova " +result.getRunCount());
		
		l.info("Broj uspesno izvedenih testova "+ (result.getRunCount() - result.getFailureCount() - result.getIgnoreCount() - result.getFailureCount()));
		l.info("Broj palih testova "+ result.getFailureCount());
		l.info("Broj preskocenih testova "+result.getIgnoreCount());
		l.info("Broj dinamicki preskocenih testova "+result.getAssumptionFailureCount());
		//da li su svi testovi uspesno izvrseni 
		if(result.wasSuccessful()) {
			l.info("Svi testovi su uspesno izvrseni");
		}else
		{
			l.info("Postoje greske u testovima");
		}
	}
}

------------------------------------------------------------------------------------------------------------------------------------------------------
 *DODAJ* 
 KLASA ZA DODAVANJE
 
 
private AutoGuma AG;
private VulkanizerskaRadnja VR;
	
	//provera da li je OS Windows
	@BeforeAll
	public static void proveriOperativniSistem() {
		assertTrue(System.getProperty("os.name").contains("Windows"));
	}
	
	//inicijalizacija
	@BeforeEach
	void init() {
		VR = new VulkanizerskaRadnja();
	}
	@AfterEach
	void destroy() {
		VR = null;
	}
	
	//parametrizovan test za dodajGumu 
	//mora da te obezbedi odakle dolaze arg
	
	@ParameterizedTest
	@MethodSource("gume")
	void dodajGumu(AutoGuma AG) {
		if(AG == null) {
			assertThrows(NullPointerException.class, ()->VR.dodajGumu(AG));
		}
		else if(VR.gume.contains(AG)) {
			assertThrows(RuntimeException.class, ()->VR.dodajGumu(AG));
		}
		else {
			assertFalse(VR.gume.contains(AG));
			VR.dodajGumu(AG);
			assertTrue(VR.gume.contains(AG));
		}
	}
	
	public static Collection<Object[]>gume(){
		return Arrays.asList(new Object[][]{
			{null},
			{new AutoGuma("Michelin", true, 18, 180, 40)},
			{new AutoGuma("Michelin", true, 18, 180, 40)},
			{new AutoGuma("Michelin", true, 18, 190, 40)},
			{new AutoGuma("Pirelli", true, 19, 170, 30)}	
		});
	}
	
  -----------------------------------------------------------------------------------------------------------------------------------------------------
*PRONADJI*
KLASA ZA PROLAZAK


private AutoGuma AG;
	private VulkanizerskaRadnja VR;
	LinkedList<AutoGuma>novaLista = new LinkedList<>();
	
	@ParameterizedTest
	@MethodSource("metoda2")
	void pronadjiGumu(AutoGuma AG) {
		String marka = null;
		if(marka == null) {
			assertNull(VR.pronadjiGumu(marka));
		}
		LinkedList<AutoGuma> novaLista = new LinkedList<AutoGuma>();
		VR.gume.add(AG);
		assertEquals(VR.gume, VR.pronadjiGumu("Kama"));
		
		LinkedList<AutoGuma> novaLista1 = new LinkedList<AutoGuma>();
		VR.gume.add(AG);
		assertNotEquals(VR.gume, VR.pronadjiGumu("ABC"));
		
	}
	
	
	public static Collection<Object[]>metoda2(){
		return Arrays.asList(new Object[][] {
			{new AutoGuma("Kama", true, 18, 190, 42)},
			{new AutoGuma("Michelin", true, 18, 180, 40)},
			{new AutoGuma("Sava", true, 17, 175, 39)},
			{new AutoGuma("Michelin", true, 18, 180, 40)},
			{new AutoGuma("Kama", true, 18, 190, 42)}
		});
	}

	
-----------------------------------------------------------------------------------------------------------------------------
//////////////////////////////////////////////////////////////////////////////////////////////////////////
	
	@RunWith(Parameterized.class)
public class VulkanizerskaRadnjaDodajGumuParametrizedTest {

	private AutoGuma AG;
	private VulkanizerskaRadnja VR;
	
	@BeforeClass
	public static void ProveriOperativniSistem() {
		Assume.assumeTrue(System.getProperty("os.name").contains("Windows"));
	}
	
	@Rule
	public final TestRule timeout = Timeout.seconds(5);

	public VulkanizerskaRadnjaDodajGumuParametrizedTest(AutoGuma AG) {
		this.AG = AG;
	}

	@Parameters
	public static Collection<Object[]> gume() {
		return Arrays.asList(new Object[][] {
			{new AutoGuma("Michelin", true, 18, 180, 40)},
			{new AutoGuma("Michelin", true, 18, 180, 40)},
			{new AutoGuma("Michelin", true, 18, 180, 40)},
			{new AutoGuma("Pireli", false, 19,170, 30)}
		});
	}
	
	@Before
	public void init() {
		VR = new VulkanizerskaRadnja();
	}

	@Test(expected = NullPointerException.class)
	public void dodajGumuTest() {
		AG = null;
		VR.dodajGumu(AG);
	}

	@Test(expected = RuntimeException.class)
	public void dodajGumuTest2() {
		VR.dodajGumu(AG);
		VR.dodajGumu(AG);
	}

	@Test
	public void dodajGumuTest3() {
		assertFalse(VR.gume.contains(AG));
		VR.dodajGumu(AG);
		assertTrue(VR.gume.contains(AG));
	}
	
	@After
	public void destroy() {
		VR = null;
	}

}
----------------------------------------------------------------------------------------------------------------------

@RunWith(Parameterized.class)
public class VulkanizerskaRadnjaPronadjiGumuParazmetrizedTest {

	private AutoGuma AG;
	private VulkanizerskaRadnja VR;
	
	@BeforeClass
	public static void ProveriOperativniSistem() {
		Assume.assumeTrue(System.getProperty("os.name").contains("Windows"));
	}
	

	public VulkanizerskaRadnjaPronadjiGumuParazmetrizedTest(AutoGuma AG) {
		this.AG = AG;
	}

	@Parameters
	public static Collection<Object[]> gume() {
		return Arrays.asList(new Object[][] {
			{new AutoGuma("Michelin", true, 18, 180, 40)},
			{new AutoGuma("Michelin", true, 18, 185, 45)},
			{new AutoGuma("Michelin", true, 18, 190, 40)},
			{new AutoGuma("Michelin", false, 19,170, 30)}
		});
	}
	
	@Before
	public void init() {
		VR = new VulkanizerskaRadnja();
	}

	@Test
	public void dodajGumuTest() {
		String marka = null;
		assertNull(VR.pronadjiGumu(marka));
	}

	@Test
	public void dodajGumuTest2() {
		assertFalse(VR.gume.contains(AG));
		VR.dodajGumu(AG);
		LinkedList<AutoGuma> gume = new LinkedList<AutoGuma>();
		gume.add(AG);
		assertEquals(gume, VR.pronadjiGumu("Michelin"));
	}
	
	@Test
	public void dodajGumuTest3() {
		assertFalse(VR.gume.contains(AG));
		VR.dodajGumu(AG);
		LinkedList<AutoGuma> gumes = new LinkedList<AutoGuma>();
		gumes.add(AG);
		assertNotEquals(gumes, VR.pronadjiGumu("Pireli - NotExistingModel"));
	}
	
	@After
	public void destroy() {
		VR = null;
	}

}
