# Valorant Page Guard
- Concept by CompiledCode
- English fix by Carlos & Google Translate

**Note: If you do this wrong, there is a chance for detection! The game must think everything is normal.**
**Note: This alone no is full bypass since valorant is log each read address**

## Preface 
Riot Games attempts to minimize the use of external cheats by applying PAGE_GUARD to an empty page and upon accessing this page will redirect to the correct pointer.

## How does this minimize external cheats?
PAGE_GUARD (https://docs.microsoft.com/it-it/windows/win32/memory/creating-guard-pages) will cause an access violation when attemping to read/write/execute anything on the guarded page.  Valorant creates pages solely for the reason of applying PAGE_GUARD.  Furthermore, Valorant registers a Vectored Exception Handler, also known as as VEH.

## What does this VEH do?
Valorant's VEH will catch the exception caused by reading the guarded pages and based on the page read, it will set the value into the correct non-guarded pointer.

## How can I bypass this?
Due to the exception the guarded page causes, reading virtual memory externally will not work.  But wait; Could I not translate the virtual address to physical and map the memory?  Smart idea!  Unfortunately, because of the empty pages, you will not be reading the correct pointer.  Most Valorant cheats inject a dll into the game so upon reading these guarded pages, valorant will set the correct pointer.  What if I told you by using two lines of assembly code, we can bypass the entire system.  If DLLs can read the memory fine, we can emulate this.

## Writing the bypass
Firstly, you can either allocate page(s) or scan Valorant for usable executable and writable pages.  The same page with both writable and executable or different pages will work.  By writing these two instructions, (see figure 1) we have bypassed the guarded page system Valorant uses.  In the image, you will see an address (gworld from decryption) and a blue box (hidden address).  In the executable page you found or allocated, you will write the **mov rax, [in]** instruction.  This will dereference the guarded region address within Valorant triggering the exception handler and setting **rax** to the correct pointer.  In the second line of assembly, **mov [out], rax**, you will specify the out address (writable page) where it will apply the proper pointer.  Your driver can then read ther correct pointer.  Voila! You have read a value protected by PAGE_GUARD externally.

**Note: it is your job call the code, you will need a return instruction**

![Figure One](https://i.imgur.com/fQjG8Bn.png)
